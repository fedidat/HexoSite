---
title: 'Jenkins and Docker: "authentication required"'
date: 2018-11-04 07:20:47
tags:
---

My new CI/CD Jenkins pipelines at work could not push images to our Openshift registry, failing with the error "authentication required".

As I once mentioned, I generally prefer scripted pipelines due to the flexibility of Groovy and the code-as-configuration aspect. However for Docker, I prefer using the nicer declarative syntax. So my `docker` stage looked like this:

```groovy
stage('docker') {
    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-registry', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
        sh "sudo docker login -p $PASSWORD -u $USERNAME registry.address"
    }
    docker.withRegistry('https://registry.address', 'docker-registry') {
        docker.build(imageName).push();
    }
}
```

The login step kept succeeding with "Login successful" while the second stage failed.

As I usually do with Jenkins, I first try to translate the syntax to scripted pipelines that are then reproducible in a shell. Here is the second stage rewritten:

```groovy
    docker.withRegistry('https://registry.address', 'docker-registry') {
        sh "docker build -t ${imageName} ."
        sh "docker push ${imageName}"
    }
```

This failed again. So I headed over to the shell to run these commands, which failed again. I finally realized that the login command ran with `sudo` whereas the push command did not. After fixing it by making both stages run with `sudo`, it worked! Although I could also have removed the `sudo` on the login step instead.

What I learned is that, once again, declarative pipelines are unreliable because you cannot know what code really lies behind the configuration like you would with scripted pipelines.