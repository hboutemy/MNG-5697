Dependency Type Bundle using Artifact Handler From Extension Plugin
======

Declare `maven-bundle-plugin` as extension to be able to use a dependency with type=bundle:

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.felix</groupId>
            <artifactId>org.apache.felix.webconsole</artifactId>
            <version>4.8.8</version>
            <type>bundle</type>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.felix</groupId>
                <artifactId>maven-bundle-plugin</artifactId>
                <version>5.1.1</version>
                <extensions>true</extensions>
            </plugin>
        </plugins>
    </build>
```

result:

```
$ mvn package
mvn package
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------< test.bundle.dependency.type:success >-----------------
[INFO] Building Expected successful dependency type=bundle 1.0.0-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/felix/org.apache.felix.webconsole/4.8.8/org.apache.felix.webconsole-4.8.8.jar
Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/felix/org.apache.felix.webconsole/4.8.8/org.apache.felix.webconsole-4.8.8.jar (541 kB at 3.7 MB/s)
[INFO] 
[INFO] --- resources:3.3.0:resources (default-resources) @ success ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/herve/dev/maven/misc/MNG-5697/2-dependency-type-success/src/main/resources
[INFO] 
[INFO] --- compiler:3.10.1:compile (default-compile) @ success ---
[INFO] No sources to compile
[INFO] 
[INFO] --- resources:3.3.0:testResources (default-testResources) @ success ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /home/herve/dev/maven/misc/MNG-5697/2-dependency-type-success/src/test/resources
[INFO] 
[INFO] --- compiler:3.10.1:testCompile (default-testCompile) @ success ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.0.0:test (default-test) @ success ---
[INFO] No tests to run.
[INFO] 
[INFO] --- jar:3.3.0:jar (default-jar) @ success ---
[WARNING] JAR will be empty - no content was marked for inclusion!
[INFO] Building jar: /home/herve/dev/maven/misc/MNG-5697/2-dependency-type-success/target/success-1.0.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.902 s
[INFO] Finished at: 2023-06-10T07:42:39+02:00
[INFO] ------------------------------------------------------------------------
```

Using the artifact handler for `bundle` type provided by the plugin as extension, resolver knows that dependency with type=`bundle` means a file with `jar` extension (and every other customisations of the artifact handler).

Notice: given how the bundle artifact handlers looks like jar one, not sure many users do teh effort to declare dependencies as bundle type.
