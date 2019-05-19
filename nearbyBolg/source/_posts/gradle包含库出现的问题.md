---
title: gradle包含库出现的问题
date: 2016-10-21 10:41:37
categories: gradle
tags: [android,gradle]
---
Error:org.gradle.api.internal.changedetection.state.DefaultFileCollectionSnapshotter$FileCollectionSnapshotImpl cannot be cast to org.gradle.api.internal.changedetection.state.OutputFilesCollectionSnapshotter$OutputFilesSnapshot
Possible causes for this unexpected error include:<ul><li>Gradle's dependency cache may be corrupt (this sometimes occurs after a network connection timeout.)
<a href="syncProject">Re-download dependencies and sync project (requires network)</a></li><li>The state of a Gradle build process (daemon) may be corrupt. Stopping all Gradle daemons may solve this problem.
<a href="stopGradleDaemons">Stop Gradle build processes (requires restart)</a></li><li>Your project may be using a third-party plugin which is not compatible with the other plugins in the project or the version of Gradle requested by the project.</li></ul>In the case of corrupt Gradle processes, you can also try closing the IDE and then killing all Java processes.


 我直接把根目录的build删除，.gradle个文件夹也删除，然后gradle文件夹里面的版本再次提升一下版本就好了 

