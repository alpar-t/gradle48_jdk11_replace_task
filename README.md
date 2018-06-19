# gradle48_jdk11_replace_task
Reproduce issue with Gradle 4.8 and JDK 11

Issues
======
- https://github.com/gradle/gradle/issues/5730
- https://github.com/gradle/gradle/issues/5731

Replace test task
-----------------

Behavior is non deterministic.
See the following (No changes in-between).
```
[:~/work/elastic/gradle48_jdk11_replace_task] master(+122/-0,2) ± ./gradlew clean check -s

> Configure project :
test task is now class RandomizedTestingTask_Decorated
Gradle 4.8
10.0.1 (Oracle Corporation 10.0.1+10)
Found the following `test` tasks
:test -> class org.gradle.api.tasks.testing.Test_Decorated

> Task :clean
> Task :compileJava
> Task :processResources NO-SOURCE
> Task :classes
> Task :compileTestJava
> Task :processTestResources NO-SOURCE
> Task :testClasses
> Task :test
> Task :check

BUILD SUCCESSFUL in 0s
4 actionable tasks: 4 executed
[:~/work/elastic/gradle48_jdk11_replace_task] master(+122/-0,2) ± ./gradlew clean check

> Configure project :
test task is now class RandomizedTestingTask_Decorated
Gradle 4.8
10.0.1 (Oracle Corporation 10.0.1+10)
Found the following `test` tasks
:test -> class RandomizedTestingTask_Decorated

> Task :clean
> Task :compileJava
> Task :processResources NO-SOURCE
> Task :classes
> Task :compileTestJava
> Task :processTestResources NO-SOURCE
> Task :testClasses

> Task :test
Running tests
random action

> Task :check

BUILD SUCCESSFUL in 0s
4 actionable tasks: 4 executed

```
The goal is to replace the Gradle test task with 
http://labs.carrotsearch.com/randomizedtesting.html 

 
JDK 11 Testing
--------------

```
[:~/work/elastic/gradle48_jdk11_replace_task] % git checkout jdk11_test
[:~/work/elastic/gradle48_jdk11_replace_task] % export JAVA_HOME=/home/alpar/opt/jdk-11-ea18/
[:~/work/elastic/gradle48_jdk11_replace_task] % ./gradlew clean check -s

> Configure project :
Gradle 4.8
11-ea ("Oracle Corporation" 11-ea+18)

> Task :clean
> Task :compileJava
> Task :processResources NO-SOURCE
> Task :classes
> Task :compileTestJava
> Task :processTestResources NO-SOURCE
> Task :testClasses
> Task :test FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':test'.
> failed to read class file /home/alpar/work/elastic/gradle48_jdk11_replace_task/build/classes/java/test/Test.class

* Try:
Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Exception is:
org.gradle.api.tasks.TaskExecutionException: Execution failed for task ':test'.
	at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.executeActions(ExecuteActionsTaskExecuter.java:110)
	at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.execute(ExecuteActionsTaskExecuter.java:77)
	at org.gradle.api.internal.tasks.execution.OutputDirectoryCreatingTaskExecuter.execute(OutputDirectoryCreatingTaskExecuter.java:51)
	at org.gradle.api.internal.tasks.execution.SkipUpToDateTaskExecuter.execute(SkipUpToDateTaskExecuter.java:59)
	at org.gradle.api.internal.tasks.execution.ResolveTaskOutputCachingStateExecuter.execute(ResolveTaskOutputCachingStateExecuter.java:54)
	at org.gradle.api.internal.tasks.execution.ValidatingTaskExecuter.execute(ValidatingTaskExecuter.java:59)
	at org.gradle.api.internal.tasks.execution.SkipEmptySourceFilesTaskExecuter.execute(SkipEmptySourceFilesTaskExecuter.java:101)
	at org.gradle.api.internal.tasks.execution.FinalizeInputFilePropertiesTaskExecuter.execute(FinalizeInputFilePropertiesTaskExecuter.java:44)
	at org.gradle.api.internal.tasks.execution.CleanupStaleOutputsExecuter.execute(CleanupStaleOutputsExecuter.java:91)
	at org.gradle.api.internal.tasks.execution.ResolveTaskArtifactStateTaskExecuter.execute(ResolveTaskArtifactStateTaskExecuter.java:62)
	at org.gradle.api.internal.tasks.execution.SkipTaskWithNoActionsExecuter.execute(SkipTaskWithNoActionsExecuter.java:59)
	at org.gradle.api.internal.tasks.execution.SkipOnlyIfTaskExecuter.execute(SkipOnlyIfTaskExecuter.java:54)
	at org.gradle.api.internal.tasks.execution.ExecuteAtMostOnceTaskExecuter.execute(ExecuteAtMostOnceTaskExecuter.java:43)
	at org.gradle.api.internal.tasks.execution.CatchExceptionTaskExecuter.execute(CatchExceptionTaskExecuter.java:34)
	at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter$1.run(EventFiringTaskExecuter.java:51)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:317)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:309)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor.execute(DefaultBuildOperationExecutor.java:185)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor.run(DefaultBuildOperationExecutor.java:97)
	at org.gradle.internal.operations.DelegatingBuildOperationExecutor.run(DelegatingBuildOperationExecutor.java:31)
	at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter.execute(EventFiringTaskExecuter.java:46)
	at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$ExecuteTaskAction.execute(DefaultTaskExecutionGraph.java:262)
	at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$ExecuteTaskAction.execute(DefaultTaskExecutionGraph.java:246)
	at org.gradle.execution.taskgraph.DefaultTaskPlanExecutor$TaskExecutorWorker$1.execute(DefaultTaskPlanExecutor.java:136)
	at org.gradle.execution.taskgraph.DefaultTaskPlanExecutor$TaskExecutorWorker$1.execute(DefaultTaskPlanExecutor.java:130)
	at org.gradle.execution.taskgraph.DefaultTaskPlanExecutor$TaskExecutorWorker.execute(DefaultTaskPlanExecutor.java:201)
	at org.gradle.execution.taskgraph.DefaultTaskPlanExecutor$TaskExecutorWorker.executeWithTask(DefaultTaskPlanExecutor.java:192)
	at org.gradle.execution.taskgraph.DefaultTaskPlanExecutor$TaskExecutorWorker.run(DefaultTaskPlanExecutor.java:130)
	at org.gradle.internal.concurrent.ExecutorPolicy$CatchAndRecordFailures.onExecute(ExecutorPolicy.java:63)
	at org.gradle.internal.concurrent.ManagedExecutorImpl$1.run(ManagedExecutorImpl.java:46)
	at org.gradle.internal.concurrent.ThreadFactoryImpl$ManagedThreadRunnable.run(ThreadFactoryImpl.java:55)
Caused by: org.gradle.api.GradleException: failed to read class file /home/alpar/work/elastic/gradle48_jdk11_replace_task/build/classes/java/test/Test.class
	at org.gradle.api.internal.tasks.testing.detection.AbstractTestFrameworkDetector.classVisitor(AbstractTestFrameworkDetector.java:126)
	at org.gradle.api.internal.tasks.testing.detection.AbstractTestFrameworkDetector.processTestClass(AbstractTestFrameworkDetector.java:145)
	at org.gradle.api.internal.tasks.testing.detection.AbstractTestFrameworkDetector.processTestClass(AbstractTestFrameworkDetector.java:136)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestClassScanner$1.visitClassFile(DefaultTestClassScanner.java:58)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestClassScanner$ClassFileVisitor.visitFile(DefaultTestClassScanner.java:76)
	at org.gradle.api.internal.file.collections.jdk7.Jdk7DirectoryWalker$1.visitFile(Jdk7DirectoryWalker.java:86)
	at org.gradle.api.internal.file.collections.jdk7.Jdk7DirectoryWalker$1.visitFile(Jdk7DirectoryWalker.java:59)
	at org.gradle.api.internal.file.collections.jdk7.Jdk7DirectoryWalker.walkDir(Jdk7DirectoryWalker.java:59)
	at org.gradle.api.internal.file.collections.DirectoryFileTree.walkDir(DirectoryFileTree.java:154)
	at org.gradle.api.internal.file.collections.DirectoryFileTree.visitFrom(DirectoryFileTree.java:132)
	at org.gradle.api.internal.file.collections.DirectoryFileTree.visit(DirectoryFileTree.java:117)
	at org.gradle.api.internal.file.collections.FileTreeAdapter.visit(FileTreeAdapter.java:117)
	at org.gradle.api.internal.file.CompositeFileTree.visit(CompositeFileTree.java:93)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestClassScanner.detectionScan(DefaultTestClassScanner.java:56)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestClassScanner.run(DefaultTestClassScanner.java:50)
	at org.gradle.api.internal.tasks.testing.processors.TestMainAction.run(TestMainAction.java:55)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestExecuter.execute(DefaultTestExecuter.java:116)
	at org.gradle.api.internal.tasks.testing.detection.DefaultTestExecuter.execute(DefaultTestExecuter.java:51)
	at org.gradle.api.tasks.testing.AbstractTestTask.executeTests(AbstractTestTask.java:469)
	at org.gradle.api.tasks.testing.Test.executeTests(Test.java:603)
	at jdk.internal.reflect.GeneratedMethodAccessor338.invoke(Unknown Source)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at org.gradle.internal.reflect.JavaMethod.invoke(JavaMethod.java:73)
	at org.gradle.api.internal.project.taskfactory.StandardTaskAction.doExecute(StandardTaskAction.java:46)
	at org.gradle.api.internal.project.taskfactory.StandardTaskAction.execute(StandardTaskAction.java:39)
	at org.gradle.api.internal.project.taskfactory.StandardTaskAction.execute(StandardTaskAction.java:26)
	at org.gradle.api.internal.AbstractTask$TaskActionWrapper.execute(AbstractTask.java:794)
	at org.gradle.api.internal.AbstractTask$TaskActionWrapper.execute(AbstractTask.java:761)
	at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter$1.run(ExecuteActionsTaskExecuter.java:131)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:317)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor$RunnableBuildOperationWorker.execute(DefaultBuildOperationExecutor.java:309)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor.execute(DefaultBuildOperationExecutor.java:185)
	at org.gradle.internal.operations.DefaultBuildOperationExecutor.run(DefaultBuildOperationExecutor.java:97)
	at org.gradle.internal.operations.DelegatingBuildOperationExecutor.run(DelegatingBuildOperationExecutor.java:31)
	at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.executeAction(ExecuteActionsTaskExecuter.java:120)
	at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.executeActions(ExecuteActionsTaskExecuter.java:99)
	... 30 more
Caused by: java.lang.IllegalArgumentException: Unsupported class file major version 55
	at org.objectweb.asm.ClassReader.<init>(ClassReader.java:166)
	at org.objectweb.asm.ClassReader.<init>(ClassReader.java:148)
	at org.objectweb.asm.ClassReader.<init>(ClassReader.java:136)
	at org.gradle.api.internal.tasks.testing.detection.AbstractTestFrameworkDetector.classVisitor(AbstractTestFrameworkDetector.java:123)
	... 65 more


* Get more help at https://help.gradle.org

BUILD FAILED in 0s
4 actionable tasks: 4 executed

```

