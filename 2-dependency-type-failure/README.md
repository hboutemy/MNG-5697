Expected Failure: Missing Artifact Handler
======

Intentionally did not declare `maven-bundle-plugin` but used a dependency with type=bundle:

```xml
        <dependency>
            <groupId>org.apache.felix</groupId>
            <artifactId>org.apache.felix.webconsole</artifactId>
            <version>4.8.8</version>
            <type>bundle</type>
        </dependency>
```

expected failure:

```
$ mvn package
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< test.bundle.dependency.type:fail >------------------
[INFO] Building Expected failing dependency type=bundle 1.0.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/felix/org.apache.felix.webconsole/4.8.8/org.apache.felix.webconsole-4.8.8.pom
Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/felix/org.apache.felix.webconsole/4.8.8/org.apache.felix.webconsole-4.8.8.pom (19 kB at 167 kB/s)
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/felix/org.apache.felix.webconsole/4.8.8/org.apache.felix.webconsole-4.8.8.bundle
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.422 s
[INFO] Finished at: 2023-06-10T07:35:12+02:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project fail: Could not resolve dependencies for project test.bundle.dependency.type:fail:jar:1.0.0-SNAPSHOT: The following artifacts could not be resolved: org.apache.felix:org.apache.felix.webconsole:bundle:4.8.8 (absent): Could not find artifact org.apache.felix:org.apache.felix.webconsole:bundle:4.8.8 in central (https://repo.maven.apache.org/maven2) -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
```

Given there is no artifact handler for `bundle` type, resolver tries to download a file with `bundle` extension as if there was a default artifact handler that takes the type as file name extension.
