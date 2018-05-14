---
title: 'Bamboo: SCPRemoteException: is a directory'
date: 2018-05-14 19:32:12
tags:
---

Recently at work, I was reworking a build plan in Atlassian Bamboo. I got an error with an SCP task.

This is the stack trace I got:

```
DD-MM-2018 16:07:28 	Starting task 'SCP Task' of type 'com.atlassian.bamboo.plugins.bamboo-scp-plugin:scptask'
DD-MM-2018 16:07:28 	Uploading '/opt/atlassian/bamboo-home/xml-data/build-dir/myplan/myfile.jar'...
DD-MM-2018 16:07:28 	Failed to upload file '/myfile.jar'
DD-MM-2018 16:07:28 	net.schmizz.sshj.xfer.scp.SCPException: Remote SCP command had error: scp: /tmp/myproject/1.1.18-SNAPSHOT-434/: Is a directory
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPEngine.check(SCPEngine.java:90)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPEngine.sendMessage(SCPEngine.java:154)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPUploadClient.sendFile(SCPUploadClient.java:92)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPUploadClient.process(SCPUploadClient.java:73)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPUploadClient.startCopy(SCPUploadClient.java:65)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPUploadClient.copy(SCPUploadClient.java:45)
DD-MM-2018 16:07:28 	        at net.schmizz.sshj.xfer.scp.SCPFileTransfer.upload(SCPFileTransfer.java:52)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.plugins.scp.ScpTask.transferFiles(ScpTask.java:199)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.plugins.scp.ScpTask.execute(ScpTask.java:97)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.task.TaskExecutorImpl.executeTasks(TaskExecutorImpl.java:188)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.task.TaskExecutorImpl.execute(TaskExecutorImpl.java:94)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.build.pipeline.tasks.ExecuteBuildTask.call(ExecuteBuildTask.java:87)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.v2.build.agent.DefaultBuildAgent.build(DefaultBuildAgent.java:206)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.v2.build.agent.BuildAgentControllerImpl.waitAndPerformBuild(BuildAgentControllerImpl.java:103)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.v2.build.agent.DefaultBuildAgent$1.run(DefaultBuildAgent.java:111)
DD-MM-2018 16:07:28 	        at com.atlassian.bamboo.build.pipeline.concurrent.NamedThreadFactory$2.run(NamedThreadFactory.java:52)
DD-MM-2018 16:07:28 	        at java.lang.Thread.run(Thread.java:722)
DD-MM-2018 16:07:28 	Copy Failed. Some files were not uploaded successfully.
```

Which is weird. "Is a directory"? Isn't the destination of an SCP task supposed to be a directory?

Turns out this is a [long standing issue](https://jira.atlassian.com/browse/BAM-13209) that still hasn't gotten resolved. I got thrown off the scent because the name of the exception class is different - SCPRemoteException instead of SCPException.

Long story short, the issue isn't that the destination "is a directory", but precisely that it does not exist. Which is the opposite meaning of the message.

Both the counter-intuitive nature of the message and the huge time span that this issue has managed to stay open are revealing of the flaws of Atlassian Bamboo in my opinion. It seems like the company has completely given up here, in favor of its competitors e.g Jenkins, Travis-CI, CircleCI, etc... There is a project - Bitbucket Pipeline - supposed to replace it but it's just not up to the task.

Next week I should start working on migrating our CI to Jenkins and I hope to write some posts on the process or on interesting aspects.