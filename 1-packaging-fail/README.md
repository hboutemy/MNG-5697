Expected Failure: Missing Packaging
======

Intentionally declared `maven-bundle-plugin` without `<extensions>true</extensions>`

```
$ mvn package
[INFO] Scanning for projects...
[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[ERROR] Unknown packaging: bundle @ line 27, column 16
 @ 
[ERROR] The build could not read 1 project -> [Help 1]
[ERROR]   
[ERROR]   The project test.bundle.packaging:fail:1.0.0-SNAPSHOT (/home/herve/dev/maven/misc/MNG-5697/1-packaging-fail/pom.xml) has 1 error
[ERROR]     Unknown packaging: bundle @ line 27, column 16
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
```

## Deep Dive into Classloaders

simplified output

```
$ mvn -X package
Apache Maven 3.9.2 (c9616018c7a021c1c39be70fb2843d6f5f9b8a1c)
Maven home: /home/herve/.sdkman/candidates/maven/current
Java version: 11.0.16, vendor: Azul Systems, Inc., runtime: /home/herve/.sdkman/candidates/java/11.0.16-zulu
Default locale: fr_FR, platform encoding: UTF-8
OS name: "linux", version: "5.15.0-73-generic", arch: "amd64", family: "unix"
[DEBUG] Created new class realm maven.api
[DEBUG] Importing foreign packages into class realm maven.api
[DEBUG]   Imported: javax.annotation.* < plexus.core
...
[INFO] Scanning for projects...
[DEBUG] Extension realms for project test.bundle.packaging:fail:bundle:1.0.0-SNAPSHOT: (none)
[DEBUG] Looking up lifecycle mappings for packaging bundle from ClassRealm[plexus.core, parent: null]
[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[ERROR] Unknown packaging: bundle @ line 27, column 16
...
```

complete output

```
$ mvn -X package
Apache Maven 3.9.2 (c9616018c7a021c1c39be70fb2843d6f5f9b8a1c)
Maven home: /home/herve/.sdkman/candidates/maven/current
Java version: 11.0.16, vendor: Azul Systems, Inc., runtime: /home/herve/.sdkman/candidates/java/11.0.16-zulu
Default locale: fr_FR, platform encoding: UTF-8
OS name: "linux", version: "5.15.0-73-generic", arch: "amd64", family: "unix"
[DEBUG] Created new class realm maven.api
[DEBUG] Importing foreign packages into class realm maven.api
[DEBUG]   Imported: javax.annotation.* < plexus.core
[DEBUG]   Imported: javax.annotation.security.* < plexus.core
[DEBUG]   Imported: javax.inject.* < plexus.core
[DEBUG]   Imported: org.apache.maven.* < plexus.core
[DEBUG]   Imported: org.apache.maven.artifact < plexus.core
[DEBUG]   Imported: org.apache.maven.classrealm < plexus.core
[DEBUG]   Imported: org.apache.maven.cli < plexus.core
[DEBUG]   Imported: org.apache.maven.configuration < plexus.core
[DEBUG]   Imported: org.apache.maven.exception < plexus.core
[DEBUG]   Imported: org.apache.maven.execution < plexus.core
[DEBUG]   Imported: org.apache.maven.execution.scope < plexus.core
[DEBUG]   Imported: org.apache.maven.graph < plexus.core
[DEBUG]   Imported: org.apache.maven.lifecycle < plexus.core
[DEBUG]   Imported: org.apache.maven.model < plexus.core
[DEBUG]   Imported: org.apache.maven.monitor < plexus.core
[DEBUG]   Imported: org.apache.maven.plugin < plexus.core
[DEBUG]   Imported: org.apache.maven.profiles < plexus.core
[DEBUG]   Imported: org.apache.maven.project < plexus.core
[DEBUG]   Imported: org.apache.maven.reporting < plexus.core
[DEBUG]   Imported: org.apache.maven.repository < plexus.core
[DEBUG]   Imported: org.apache.maven.rtinfo < plexus.core
[DEBUG]   Imported: org.apache.maven.settings < plexus.core
[DEBUG]   Imported: org.apache.maven.toolchain < plexus.core
[DEBUG]   Imported: org.apache.maven.usability < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.* < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.authentication < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.authorization < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.events < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.observers < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.proxy < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.repository < plexus.core
[DEBUG]   Imported: org.apache.maven.wagon.resource < plexus.core
[DEBUG]   Imported: org.codehaus.classworlds < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.* < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.classworlds < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.component < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.configuration < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.container < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.context < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.lifecycle < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.logging < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.personality < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.util.xml.Xpp3Dom < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.util.xml.pull.XmlPullParser < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.util.xml.pull.XmlPullParserException < plexus.core
[DEBUG]   Imported: org.codehaus.plexus.util.xml.pull.XmlSerializer < plexus.core
[DEBUG]   Imported: org.eclipse.aether.* < plexus.core
[DEBUG]   Imported: org.eclipse.aether.artifact < plexus.core
[DEBUG]   Imported: org.eclipse.aether.collection < plexus.core
[DEBUG]   Imported: org.eclipse.aether.deployment < plexus.core
[DEBUG]   Imported: org.eclipse.aether.graph < plexus.core
[DEBUG]   Imported: org.eclipse.aether.impl < plexus.core
[DEBUG]   Imported: org.eclipse.aether.installation < plexus.core
[DEBUG]   Imported: org.eclipse.aether.internal.impl < plexus.core
[DEBUG]   Imported: org.eclipse.aether.metadata < plexus.core
[DEBUG]   Imported: org.eclipse.aether.repository < plexus.core
[DEBUG]   Imported: org.eclipse.aether.resolution < plexus.core
[DEBUG]   Imported: org.eclipse.aether.spi < plexus.core
[DEBUG]   Imported: org.eclipse.aether.transfer < plexus.core
[DEBUG]   Imported: org.eclipse.aether.util < plexus.core
[DEBUG]   Imported: org.eclipse.aether.version < plexus.core
[DEBUG]   Imported: org.fusesource.jansi.* < plexus.core
[DEBUG]   Imported: org.slf4j.* < plexus.core
[DEBUG]   Imported: org.slf4j.event.* < plexus.core
[DEBUG]   Imported: org.slf4j.helpers.* < plexus.core
[DEBUG]   Imported: org.slf4j.spi.* < plexus.core
[DEBUG] Populating class realm maven.api
[DEBUG] Created adapter factory; available factories [file-lock, rwlock-local, semaphore-local, noop]; available name mappers [discriminating, file-gav, file-hgav, file-static, gav, static]
[INFO] Error stacktraces are turned on.
[DEBUG] Message scheme: color
[DEBUG] Message styles: debug info warning error success failure strong mojo project
[DEBUG] Reading global settings from /home/herve/.sdkman/candidates/maven/current/conf/settings.xml
[DEBUG] Reading user settings from /home/herve/.m2/settings.xml
[DEBUG] Reading global toolchains from /home/herve/.sdkman/candidates/maven/current/conf/toolchains.xml
[DEBUG] Reading user toolchains from /home/herve/.m2/toolchains.xml
[DEBUG] Using local repository at /home/herve/.m2/repository
[DEBUG] Using manager EnhancedLocalRepositoryManager with priority 10.0 for /home/herve/.m2/repository
[INFO] Scanning for projects...
[DEBUG] Extension realms for project test.bundle.packaging:fail:bundle:1.0.0-SNAPSHOT: (none)
[DEBUG] Looking up lifecycle mappings for packaging bundle from ClassRealm[plexus.core, parent: null]
[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[ERROR] Unknown packaging: bundle @ line 27, column 16
 @ 
[ERROR] The build could not read 1 project -> [Help 1]
org.apache.maven.project.ProjectBuildingException: Some problems were encountered while processing the POMs:
[ERROR] Unknown packaging: bundle @ line 27, column 16

    at org.apache.maven.project.DefaultProjectBuilder.build (DefaultProjectBuilder.java:389)
    at org.apache.maven.graph.DefaultGraphBuilder.collectProjects (DefaultGraphBuilder.java:349)
    at org.apache.maven.graph.DefaultGraphBuilder.getProjectsForMavenReactor (DefaultGraphBuilder.java:340)
    at org.apache.maven.graph.DefaultGraphBuilder.build (DefaultGraphBuilder.java:76)
    at org.apache.maven.DefaultMaven.buildGraph (DefaultMaven.java:448)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:197)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:910)
    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
    at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
    at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke (Method.java:566)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:283)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:226)
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:407)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:348)
[ERROR]   
[ERROR]   The project test.bundle.packaging:fail:1.0.0-SNAPSHOT (/home/herve/dev/maven/misc/MNG-5697/1-packaging-fail/pom.xml) has 1 error
[ERROR]     Unknown packaging: bundle @ line 27, column 16
[ERROR] 
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
[DEBUG] Shutting down adapter factory; available factories [file-lock, rwlock-local, semaphore-local, noop]; available name mappers [discriminating, file-gav, file-hgav, file-static, gav, static]
[DEBUG] Shutting down 'file-lock' factory
[DEBUG] Shutting down 'rwlock-local' factory
[DEBUG] Shutting down 'semaphore-local' factory
[DEBUG] Shutting down 'noop' factory
