---
title: Secret text in Jenkinsfile scripted pipelines
date: 2018-06-11 07:33:19
tags:
---

Scripted pipelines are great for using Jenkinsfile but Jenkins' [documentation](https://jenkins.io/doc/book/pipeline/jenkinsfile/#for-secret-text-usernames-and-passwords-and-secret-files) doesn't tell how to use credentials with them. And trying to use the credentials function listed there won't work.

In short, this is what does work: 

1. First setup your secret text in the Credentials category like so (if you don't have the credentials category make sure you have the Credentials plugin installed and enabled):

	1. Open the credentials category:

    ![Open credentials](/images/270-jenkinsfile-scripted-secret-text/creds.png)

	1. Add a new credential:
    
    ![Add a new credential](/images/270-jenkinsfile-scripted-secret-text/newcred.png)

	1. Enter the info and confirm:
    
    ![Enter the info and confirm](/images/270-jenkinsfile-scripted-secret-text/addcred.png)


2. Then in your Jenkinsfile use the following:

```groovy
node {
    withCredentials([string(credentialsId: 'my-secret', variable: 'SECRET') { //set SECRET with the credential content
        echo "My secret text is '${SECRET}'"
    }
}
```

Output:

    My password is '****'

