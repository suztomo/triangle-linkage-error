# Java Diamond Dependency hidden by class path element order

This repsitory demonstrates how class path element order affects
Java diamond dependency issues.

There are 5 artifacts. Artifact 4 and Artifact 5 are there just to avoid compilation errors.

Artifact 1 to 3 are there to try to generate a linkage error:

Artifact 1

```
C c = new C();
c.foo();
``` 

Artifact 2

```
public class C extends P{}
```

Artifact 3

```
public class P{}
```

However `mvn exec` command in `main` directory just works fine:

```
suztomo@suxtomo24:~/workspace/triangle-linkage-error/main$  mvn exec:java -Dexec.mainClass="A"
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building main 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- exec-maven-plugin:1.6.0:java (default-cli) @ main ---
artifact4:C.foo()
Hello A
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

Maven dependency tree plugin in the `main` directory shows the following dependency tree.

```
suztomo@suxtomo24:~/workspace/triangle-linkage-error/main$ mvn dependency:tree -Dverbose
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building main 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ main ---
[INFO] suztomo:main:jar:0.0.1-SNAPSHOT
[INFO] +- suztomo:artifact1:jar:0.0.1-SNAPSHOT:compile
[INFO] |  \- suztomo:artifact4:jar:0.0.1-SNAPSHOT:compile
[INFO] +- suztomo:artifact2:jar:0.0.1-SNAPSHOT:compile
[INFO] |  \- suztomo:artifact5:jar:0.0.1-SNAPSHOT:compile
[INFO] \- suztomo:artifact3:jar:0.0.1-SNAPSHOT:compile
```

When checking the class path, it turned out that `mvn exec` builds a class path in depth-first order, rather than the depth-first order used by [its dependency mediation](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Transitive_Dependencies).

```
suztomo@suxtomo24:~/workspace/triangle-linkage-error/main$ mvn -q exec:exec -Dexec.executable=echo -Dexec.args="%classpath"
/usr/local/google/home/suztomo/workspace/triangle-linkage-error/main/target/classes:
/usr/local/google/home/suztomo/.m2/repository/suztomo/artifact1/0.0.1-SNAPSHOT/artifact1-0.0.1-SNAPSHOT.jar:
/usr/local/google/home/suztomo/.m2/repository/suztomo/artifact4/0.0.1-SNAPSHOT/artifact4-0.0.1-SNAPSHOT.jar:
/usr/local/google/home/suztomo/.m2/repository/suztomo/artifact2/0.0.1-SNAPSHOT/artifact2-0.0.1-SNAPSHOT.jar:
/usr/local/google/home/suztomo/.m2/repository/suztomo/artifact5/0.0.1-SNAPSHOT/artifact5-0.0.1-SNAPSHOT.jar:
/usr/local/google/home/suztomo/.m2/repository/suztomo/artifact3/0.0.1-SNAPSHOT/artifact3-0.0.1-SNAPSHOT.jar
```


## Installation

Run "mvn install" in artifact5, artifact4, artifact3, artifact2 and artifact1 (in this order).




