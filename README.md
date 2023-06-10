Testing [MNG-5697](https://issues.apache.org/jira/browse/MNG-5697)
============

Maven core provides:

1. per-packaging (`pom`, `jar`, `ejb`, `ejb3`, `maven-plugin`, `war`, `ear`, `rar`, `par`) plugin bindings for `default` lifecycle: see [maven-core default-bindings](https://maven.apache.org/ref/3.9.2/maven-core/default-bindings.html)

2. a list of artifact handlers for typical dependency types (`pom`, `jar`, `test-jar`, `java-source`, `javadoc`, `ejb`, `ejb3`, `ejb-client`, `maven-plugin`, `war`, `ear`, `rar`, `par`): [maven-core artifact-handlers](https://maven.apache.org/ref/3.9.2/maven-core/artifact-handlers.html)

Notice: in Maven 3, these are defined in XML files loaded by Plexus, in Maven 4 it's provider code for Sisu/Guice (see [default-bindings](https://maven.apache.org/ref/4.0.0-alpha-5/maven-core/default-bindings.html) and [artifact-handlers](https://maven.apache.org/ref/4.0.0-alpha-5/maven-core/artifact-handlers.html))

MNG-5697 is about dropping definitions in core and replace with definition in packaging plugins: why would Maven core define `war`, `ear`, but not `bundle`?

Details on how to do that remain to be defined: copying definitions is one step that has been done in `maven-xxx-plugin` 3.0.0, but remaining steps leading to deleting from core and let users know how to replace remain to be defined; clearly, this won't be fully transparent, or additional mechanisms need to be added.

This repository is about testing and defining what will be the impact on users when default bindings and artifact handlers won't be provided by Maven core.

## Packaging Usage

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>xxx</groupId>
    <artifactId>xxx</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>bundle</packaging>
</project>
```

Defines which plugins will be used when building the project.

## Artifact Handlers Usage

```xml
<project>
    [...]
    <dependencies>
        <dependency>
            <groupId>xxx</groupId>
            <artifactId>xxx</artifactId>
            <version>1.0-SNAPSHOT</version>
            <type>bundle</type>
        </dependency>
    </dependencies>
</project>
```

Defines what classifier/file extension will be downloaded, if added to classpath, if includes dependencies.

## Testing on `bundle`

`bundle` packaging and dependency type ([defined by Felix `maven-bundle-plugin`](https://github.com/apache/felix-dev/blob/master/tools/maven-bundle-plugin/src/main/resources/META-INF/plexus/components.xml)) is an ideal test, because it's not defined in Maven core.

It could be done with [NiFi `nar`](https://github.com/apache/nifi-maven/blob/main/src/main/resources/META-INF/plexus/components.xml), or others...

Tests:

1. using `bundle` packaging with `maven-bundle-plugin`:

    - [1-packaging-fail](1-packaging-fail): failure when not declared as extension

    - [1-packaging-success](1-packaging-success): success when declared as extension

2. using `bundle` dependency type

    - failure when `maven-bundle-plugin` not declared, or not as extension

    - Question: why would a user define `maven-bundle-plugin` when consuming a `bundle` dependency?

3. multi-module tests

## Testing on overriding core-provided definitions

TBD: which plugin? how to differentiate plugin-provided definition from core-provided?

TODO test also with Maven 4 to check core Provider replaced by plugin's `META-INF/plexus/component.xml`

## Results

To be confirmed:

- packaging lifecycle bindings definition in plugin (for all packagings but not `pom`), using works with 2 impacts:

  1. require to define the plugin (as extension), when it was not necessary previously

  2. for `jar`, requiring `maven-jar-plugin` explicit definition seems odd: it should be there by default from core

- for artifact handlers:

  1. it forces project using dependency type to declare associated plugin: this looks really odd

  2. `java-source` and `javadoc` will require a target location (if anybody really use them as real dependency types...)


## Conclusion

- packaging: change seems reasonable on `ejb`, `ejb3`, `maven-plugin`, `war`, `ear`, `rar`, `par`, even if it's more verbose (require plugin declaration, as extension). But not on `jar`, that should remain in core to be available by default without declaring `maven-jar-plugin`.

- artifact handlers: plugin declaration requirement looks awkward.
