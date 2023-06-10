Using Packaging Declared By Plugin as Extension
======

```xml
                <plugin>
                    <groupId>org.apache.felix</groupId>
                    <artifactId>maven-bundle-plugin</artifactId>
                    <version>5.1.1</version>
                    <extensions>true</extensions>
                </plugin>
```

```
$ mvn package
[INFO] Scanning for projects...
[INFO] 
[INFO] -------------------< test.bundle.packaging:success >--------------------
[INFO] Building Expected successful packaging=bundle 1.0.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] -------------------------------[ bundle ]-------------------------------
[INFO] 
[INFO] --- resources:3.3.0:resources (default-resources) @ success ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/herve/dev/maven/misc/MNG-5697/1-packaging-success/src/main/resources
[INFO] 
[INFO] --- compiler:3.11.0:compile (default-compile) @ success ---
[INFO] No sources to compile
[INFO] 
[INFO] --- resources:3.3.0:testResources (default-testResources) @ success ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/herve/dev/maven/misc/MNG-5697/1-packaging-success/src/test/resources
[INFO] 
[INFO] --- compiler:3.11.0:testCompile (default-testCompile) @ success ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.1.1:test (default-test) @ success ---
[INFO] 
[INFO] --- bundle:5.1.1:bundle (default-bundle) @ success ---
[WARNING] Bundle test.bundle.packaging:success:bundle:1.0.0-SNAPSHOT : The JAR is empty: The instructions for the JAR named test.bundle.packaging.success did not cause any content to be included, this is likely wrong
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.744 s
[INFO] Finished at: 2023-06-10T07:20:42+02:00
[INFO] ------------------------------------------------------------------------
[WARNING] 
[WARNING] Plugin validation issues were detected in 2 plugin(s)
[WARNING] 
[WARNING]  * org.apache.felix:maven-bundle-plugin:5.1.1
[WARNING]  * org.apache.maven.plugins:maven-resources-plugin:3.3.0
[WARNING] 
[WARNING] For more or less details, use 'maven.plugin.validation' property with one of the values (case insensitive): [BRIEF, DEFAULT, VERBOSE]
[WARNING] 
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
[DEBUG] Creating adapter using nameMapper 'gav' and factory 'rwlock-local'
[DEBUG] Dependency collection stats {ConflictMarker.analyzeTime=526983, ConflictMarker.markTime=234233, ConflictMarker.nodeCount=218, ConflictIdSorter.graphTime=265471, ConflictIdSorter.topsortTime=133187, ConflictIdSorter.conflictIdCount=74, ConflictIdSorter.conflictIdCycleCount=0, ConflictResolver.totalTime=3315081, ConflictResolver.conflictItemCount=161, DfDependencyCollector.collectTime=153356451, DfDependencyCollector.transformTime=5328439}
[DEBUG] org.apache.felix:maven-bundle-plugin:jar:5.1.1
[DEBUG]    org.osgi:org.osgi.core:jar:6.0.0:compile
[DEBUG]    biz.aQute.bnd:biz.aQute.bndlib:jar:5.1.1:compile
[DEBUG]    org.slf4j:slf4j-api:jar:1.7.25:compile
[DEBUG]    org.apache.felix:org.apache.felix.bundlerepository:jar:1.6.6:compile
[DEBUG]       org.easymock:easymock:jar:2.4:compile
[DEBUG]    org.apache.felix:org.apache.felix.utils:jar:1.6.0:compile
[DEBUG]       org.osgi:org.osgi.compendium:jar:4.2.0:compile
[DEBUG]    org.apache.maven:maven-core:jar:3.3.9:compile
...
[DEBUG]       org.apache.maven.doxia:doxia-module-xdoc:jar:1.0:compile
[DEBUG]       org.apache.maven.doxia:doxia-module-xhtml:jar:1.0:compile
[DEBUG]    org.jdom:jdom:jar:1.1:compile
[DEBUG] Created new class realm extension>org.apache.felix:maven-bundle-plugin:5.1.1
[DEBUG] Importing foreign packages into class realm extension>org.apache.felix:maven-bundle-plugin:5.1.1
[DEBUG]   Imported:  < maven.api
[DEBUG] Populating class realm extension>org.apache.felix:maven-bundle-plugin:5.1.1
[DEBUG]   Included: org.apache.felix:maven-bundle-plugin:jar:5.1.1
[DEBUG]   Included: org.osgi:org.osgi.core:jar:6.0.0
[DEBUG]   Included: biz.aQute.bnd:biz.aQute.bndlib:jar:5.1.1
[DEBUG]   Included: org.apache.felix:org.apache.felix.bundlerepository:jar:1.6.6
[DEBUG]   Included: org.easymock:easymock:jar:2.4
...
[DEBUG]   Included: org.apache.maven.doxia:doxia-module-xhtml:jar:1.0
[DEBUG]   Included: org.jdom:jdom:jar:1.1
[DEBUG] Extension realms for project test.bundle.packaging:success:bundle:1.0.0-SNAPSHOT: [ClassRealm[extension>org.apache.felix:maven-bundle-plugin:5.1.1, parent: jdk.internal.loader.ClassLoaders$AppClassLoader@277050dc]]
[DEBUG] Created new class realm project>test.bundle.packaging:success:1.0.0-SNAPSHOT
[DEBUG] Populating class realm project>test.bundle.packaging:success:1.0.0-SNAPSHOT
[DEBUG] Looking up lifecycle mappings for packaging bundle from ClassRealm[project>test.bundle.packaging:success:1.0.0-SNAPSHOT, parent: ClassRealm[maven.api, parent: null]]
[DEBUG] === REACTOR BUILD PLAN ================================================
[DEBUG] Project: test.bundle.packaging:success:bundle:1.0.0-SNAPSHOT
[DEBUG] Tasks:   [package]
[DEBUG] Style:   Regular
[DEBUG] =======================================================================
[INFO] 
[INFO] -------------------< test.bundle.packaging:success >--------------------
[INFO] Building Expected successful packaging=bundle 1.0.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] -------------------------------[ bundle ]-------------------------------
...
```
