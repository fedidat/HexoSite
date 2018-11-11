---
title: Feature branches CI with Openshift and Jenkins pipeline
date: 2018-11-09 07:57:42
tags:
---

A few months ago, I setup feature branches CI with Openshift using Jenkins pipeline and Openshift client tools.

Our company is on an intranet, so this is after experience with CI platforms like Atlassian Bamboo and Bitbucket pipelines, as well as orchestration platforms like Docker Swarm and plain Kubernetes.

In my opinion, this is the best setup for private clouds, as it is mostly code as configuration and it's all open-source. So after half a year of tweaking it, I wanted to share my Jenkinsfile and development workflow.

Another important point of this setup is that it allows me to deploy previous builds, which was an issue I have had since migrating away from Atlassian Bamboo, which had a very handy deployment feature despite its numerous flaws.

## Jenkinsfile

```groovy
//Jenkinsfile parameters
groupName = my-group
projectName = my-project
ftpServer = dev-ftp-1
deploySrv = dev-test-1
registryAddress = dev-reg-1

node('agents') {
    //Build parameters for builds and/or deployments, add more if necessary
    properties([
        choice(name: 'DoBuild', choices: 'true\nfalse', description: 'Build or deploy a previous image'),
        choice(name: 'WhichBuild', choices: (env.BUILD_NUMBER.toInteger()..1).join(\n), description: 'If deploying, which previous build'),
        choice(name: 'WhereToDeploy', choices: 'feature-branch\ndev-server\nopenshift-int\nopenshift-ppd\npre-prod-tar\nNONE', description: 'Where to deploy, NONE for no deploy')
    ])

    //extract build information ahead of time
    buildNumber = env.BUILD_NUMBER
    buildName = env.BRANCH_NAME + "/" + buildNumber
    buildToDeploy = params.DoBuild == "true" ? buildNumber : params.WhichBuild
    whereToDeploy = params.whereToDeploy
    buildType = params.DoBuild == "true" ? "build" : "deploy"
    branchName = env.BRANCH_NAME.replace("/", "-").replace("_", "-").toLowerCase() //replace problematic Openshift characters
    imageName = "${registryAddress}/${groupName}/${projectName}:${branchName}"
    buildDescription = (buildType == "build" ? "Build" : "Deployment from build ${buildNumber}") + " to ${whereToDeploy} started" 

    echo buildDescription
    //Optionally implement curl-based build started notification here using buildDescription, e.g Slack

    try {
        stage('clean') {
            //Clean tasks, for example:
            deleteDir()
        }

        stage('checkout') {
            //Checkout/clone/pull tasks, for example:
            checkout scm
        }

        if(buildType == "build") {
            stage('build') {
                //Build tasks, for example:
                sh "gradle bootRepackage"
            }

            stage('test') {
                //Test tasks, for example:
                try {
                    sh "gradle test"
                } finally {
                    junit 'build/test-results/test/*.xml' //fail build if JUnit plugin reports exceptions
                }
            }

            stage('docker') {
                //Build and push the image
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-registry', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                    sh "sudo docker login -p $PASSWORD -u $USERNAME registry.address"
                }
                    docker.withRegistry('https://registry.address', 'docker-registry') {
                        sh "docker build -t ${imageName}-${buildNumber} ."
                        sh "docker push ${imageName}-${buildNumber}"
                    }
                }
            }
        }

        stage('deploy') {
            //If not deploying, finish
            if(whereToDeploy == "NONE")
                return

            if(whereToDeploy == "feature-branch" || whereToDeploy == "openshift-int" || whereToDeploy == "openshift-ppd") {
                //If deploying feature branch or int/preprod, first determine deployment name
                if(branchName == 'feature-development') deployName = "${projectName}-dev"
                if(whereToDeploy == 'openshift-int') deployName = "${projectName}-int"
                if(whereToDeploy == 'openshift-ppd') deployName = "${projectName}-ppd"
                else deployName = "${projectName}-${branchName}"

                //Then, login to Openshift, prepare the YAML files and execute oc commands
                withCredentials([string(credentialsId: 'registry-token', variable: 'TOKEN')]) {
                    sh "oc login ${registry-address} --token==$TOKEN"
                }

                sh "sed -i 's/BRANCHNAME/${deployName}/g' deploy/deployment.yml"
                sh "sed -i 's/BRANCHNAME/${deployName}/g' deploy/service.yml"
                sh "sed -i 's/BRANCHNAME/${deployName}/g' deploy/route.yml"
                sh "sed -i 's/DEPLOYNAME/${deployName}/g' deploy/deployment.yml"
                sh "sed -i 's/DEPLOYNAME/${deployName}/g' deploy/service.yml"
                sh "sed -i 's/DEPLOYNAME/${deployName}/g' deploy/route.yml"

                sh "oc project ${groupName}"
                sh "oc apply -f deploy/deployment.yml"
                sh "oc apply -f deploy/service.yml"
                sh "oc apply -f deploy/route.yml"

                //Finally, login and push the image to the registry
                docker.withRegistry('https://registry.address', 'docker-registry') {
                    sh "docker pull ${imageName}-${buildToDeploy}"
                    sh "docker tag ${imageName}-${buildToDeploy} ${imageName}"
                    sh "docker push ${imageName}" //Openshift will use the image tagged :latest
                }
            }
            else if(whereToDeploy == 'dev-server') {
                //If deploying to a simple server, use SSH to pull and run the image, then wait for healthCheck to resolve
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-registry', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'server-login', usernameVariable: 'SRVUSERNAME', passwordVariable: 'SRVPASSWORD']]) {
                        sh """
                            sshpass -p${SRVPASSWORD} ssh -o StrictKeyChecking=no -o UserKnownHostsFile=/dev/null ${SRVUSERNAME}@${deploySrv} '\
                            docker login p ${PASSWORD} -u ${USERNAME} ${registryAddress}; \
                            docker system prune -f; docker kill ${projectName}; docker rm ${projectName}; \
                            docker pull ${imageName}-${buildToDeploy}; \
                            docker run --detach --name ${projectName} --hostname=\$HOSTNAME ${imageName}-${buildToDeploy}';
                            bash -c 'until [[ "\$curl -s --output /dev/null --head --fail -w ''%{http_code}'' \
                            http://${whereToDeploy}/healthCheck )" = 200 ]] ; do echo '.'; sleep 5; done; \
                            echo 'Server is up';
                        """
                    }
                }
            }
            else if(whereToDeploy == 'pre-prod-tar') {
                //If deploying a TAR for production, pull, tag, save and upload to an FTP server
                sh """
                    docker login p ${PASSWORD} -u ${USERNAME} ${registryAddress}; \
                    docker pull ${imageName}-${buildToDeploy}; \
                    docker tag ${imageName}-${buildToDeploy} ${groupName}:${projectName}-${buildToDeploy}; \
                    docker save ${groupName}:${projectName}-${buildToDeploy} ${groupName}:${projectName}-${buildToDeploy}.tar; \
                    curl -T ${groupName}:${projectName}-${buildToDeploy}.tar ftp://${ftpServer};
                """
            }
        }

        currentBuild.result = 'SUCCESS'
    }
    catch(e) {
        throw e;
    }
    finally {
        resultString = currentBuild.result == 'SUCCESS' ? "successful" : "failed"
        resultMessage = "${projectName} build ${buildName} ${resultString}"

        //Optionally implement curl-based build finished notification here using resultMessage
    }
}
```

## Openshift: deployment/service/route.yml, Configmap

The other crucial part of this setup is the collection of files that describe the deployment. They are basically typical deployment/service/route Openshift configuration files. As an indication, these are my files:

deployment.yaml:

```yaml
apiVersion: v1
kind: DeploymentConfig
metadata:
  labels: 
    app: DEPLOYNAME
  name: DEPLOYNAME
  namespace: my-project
spec:
  replicas: 1
  selector:
    app: DEPLOYNAME
  template:
    metadata:
      labels:
        app: DEPLOYNAME
    spec:
      containers:
        - env:
          - name: PROFILE
            value: dev
          image: >- dev-reg-1/my-group/my-project:BRANCHNAME
          readinessProbe:
            httpGet:
              path: /health
              port: 8080
              initialDelaySeconds: 10
              periodSeconds: 5
              timeoutSeconds: 20
          livenessProbe:
            httpGet:
              path: /
              port: 8080
          name: my-project
          ports:
            - containerPort: 8080
              protocol: TCP
          resources:
            requests:
              cpu: 0.2
              memory: 1Gi
          volumeMounts:
            - mountPath: /etc/my-app.conf
              name: my-app-config-prop
              subPath: application.properties
        volumes:
          - configMap:
              defaultMode: 420
              name: my-app-config
            name: my-app-config-prop
triggers:
  - imageChangeParams:
      automatic: true
      containerNames:
        - gds
      from:
        kind: ImageStreamTag
        name: 'my-project:BRANCHNAME'
    type: ImageChange


```

service.yaml:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: DEPLOYNAME
  namespace: my-group
spec:
  selector:
    app: DEPLOYNAME
  ports:
  - protocol: TCP
    port: 8083
```

route.yaml:

```yaml
apiVersion: v1
kind: Route
metadata:
  name: DEPLOYNAME
  namespace: my-project
spec:
  host: DEPLOYNAME.app.osft
  to:
    kind: Service
    name: DEPLOYNAME
    weight: 100
  wildcardPolicy: None
```

Other files can be adjoined to these files, like ConfigMaps, secrets, nodeport services, etc... I have described some of these in previous articles.

## Workflow

The workflow enabled by this setup should be straightforward by now:

- Git pushes trigger a build.
- By default, builds deploy to feature branches.
- By using builds with parameters, there is an option to deploy to integration or pre-production deployments, as well as saving images to tarballs and uploading to an FTP server for separate production deployment.
- Debugging with Openshift can hardly be automated unless you want to go with one pod everywhere (among other constraints). That's why there is a choice to deploy to traditional dev servers for easier debugging.
- Many improvements can be made, such as tagging builds or using various plugins. Jenkins has endless possibilities after all. But these are mostly not relevant to this post.

There is a lot of tweaking to be done before this setup can be functional, but when it runs it's flawless.

You can also see these files on [the Github repo](https://github.com/fedidat/jenkins-openshift-ci). If you have any question or remark, leave a comment, or send me an email.