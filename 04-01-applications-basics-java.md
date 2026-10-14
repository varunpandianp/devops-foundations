# Java Build Tools — JDK, JRE, Maven, Gradle, Ant, JAR and WAR

## 1. What Is a Java Build Tool?

A Java build tool automates the process of compiling source code, resolving dependencies, running tests, packaging applications, and preparing software for deployment.

Without a build tool, developers may need to execute many commands manually. Build tools automate these repetitive tasks.

### Why do we need build tools?

Imagine a Java application contains 200 source files and depends on 15 external libraries.

Without a build tool, you may need to:
- Compile Java source files.
- Download and manage dependency JAR files.
- Configure the classpath.
- Run unit tests.
- Package compiled classes and resources.
- Create a JAR or WAR file.
- Repeat the same process for every release.

A build tool automates most of these steps through a repeatable build configuration.

### Popular Java build tools

| Tool | Description |
|---|---|
| Maven | Convention-based build and dependency management |
| Gradle | Flexible build automation using Groovy or Kotlin DSL |
| Apache Ant | Task-based build automation using XML |
| `javac` | Java compiler; not a full build automation tool |
| `java` | Runs Java applications; not a build tool |

---

## 2. Java Application Build and Execution Flow

A typical Java application follows this flow:

    Java Source Code
    (.java files)
           |
           v
    Java Compiler (javac)
           |
           v
    Bytecode
    (.class files)
           |
           v
    Package Application
    (JAR or WAR)
           |
           v
    Deploy or Run Application
           |
           v
    JVM Executes Bytecode

With Maven, Gradle, or Ant, these steps can be automated.

### Example using manual commands

Create `MyClass.java`:

    public class MyClass {
        public static void main(String[] args) {
            System.out.println("Hello KodeKloud");
        }
    }

Compile:

    javac MyClass.java

This creates:

    MyClass.class

Run:

    java MyClass

Output:

    Hello KodeKloud

**Important:** Use `java MyClass`, not `java MyClass.class`, when running a class from the classpath.

---

## 3. JDK, JRE and JVM

These three terms are related but have different purposes.

### JVM — Java Virtual Machine

The JVM executes Java bytecode.

Responsibilities include:
- Loading classes.
- Verifying bytecode.
- Executing bytecode through interpretation and JIT compilation.
- Managing memory and garbage collection.

### JRE — Java Runtime Environment

The JRE conceptually provides the runtime components required to run Java applications, including a JVM and supporting libraries.

A separate JRE package is not necessarily distributed in modern Java installations. Modern JDK distributions generally provide the runtime needed to run applications.

### JDK — Java Development Kit

The JDK provides tools required to develop Java applications.

It commonly includes:
- `javac` — Java compiler.
- `java` — Java application launcher.
- `jar` — JAR archive tool.
- `javadoc` — documentation generator.
- Other development and diagnostic tools.

### Relationship

    JDK
     |
     +-- Java development tools
     |
     +-- Runtime components
              |
              +-- JVM
              +-- Runtime libraries

This is a conceptual relationship, not a guarantee that every modern Java distribution includes a separately packaged JRE.

### Check Java installation

    java -version
    javac -version
    which java
    which javac

Check Java environment variables:

    echo "$JAVA_HOME"
    echo "$PATH"

**Remember:**
- JDK = develop and run Java applications.
- JVM = executes Java bytecode.
- JRE = runtime environment concept.

---

## 4. What Is a Dependency?

A dependency is an external library that your application needs.

Example: Your Java application needs a library for JSON processing or database connectivity.

Without a build tool, you might download the library manually and configure the classpath.

With Maven or Gradle, you declare the dependency in a configuration file. The build tool downloads it from a configured repository and makes it available during the build.

### Dependency management benefits

- Automatically downloads libraries.
- Manages library versions.
- Helps resolve transitive dependencies.
- Makes builds easier to reproduce.
- Simplifies CI/CD pipelines.

A transitive dependency is a library required by another library that your project uses.

---

## 5. Maven Build Tool

Apache Maven is a popular Java build automation and dependency management tool.

Maven uses an XML configuration file named:

    pom.xml

POM means Project Object Model.

### Maven project structure

    my-java-app/
    ├── pom.xml
    └── src/
        ├── main/
        │   ├── java/
        │   │   └── com/example/App.java
        │   └── resources/
        └── test/
            ├── java/
            └── resources/

### Important directories

| Directory | Purpose |
|---|---|
| `src/main/java` | Application source code |
| `src/main/resources` | Application resources and configuration |
| `src/test/java` | Test source code |
| `src/test/resources` | Test resources |
| `target/` | Generated build output |

### Basic Maven `pom.xml`

    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
             https://maven.apache.org/xsd/maven-4.0.0.xsd">

        <modelVersion>4.0.0</modelVersion>

        <groupId>com.example</groupId>
        <artifactId>my-java-app</artifactId>
        <version>1.0.0</version>
        <packaging>jar</packaging>

        <properties>
            <maven.compiler.release>17</maven.compiler.release>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>

    </project>

This example targets Java 17. The installed JDK and Maven compiler configuration must support the chosen release.

### Maven coordinates

- `groupId` — identifies the organization or project group.
- `artifactId` — identifies the application or library.
- `version` — identifies the release version.
- `packaging` — determines the package type, commonly `jar` or `war`.

Example:

    com.example:my-java-app:1.0.0

### Basic Maven commands

Check Maven:

    mvn -version

Compile:

    mvn compile

Run tests:

    mvn test

Package the application:

    mvn package

Clean generated build output:

    mvn clean

Clean and package:

    mvn clean package

Install the artifact in the local Maven repository:

    mvn install

Run the default lifecycle through package:

    mvn clean package

### What does `mvn clean package` do?

1. `clean` removes the previous generated build output, normally `target/`.
2. Maven processes project configuration and dependencies.
3. It compiles source code.
4. It runs the tests bound to the lifecycle, unless configured otherwise.
5. It packages the application.

For a JAR project, the output may be:

    target/my-java-app-1.0.0.jar

For a WAR project, the output may be:

    target/my-java-app-1.0.0.war

### Maven lifecycle phases

Important phases include:

| Phase | Purpose |
|---|---|
| `validate` | Check project configuration |
| `compile` | Compile main source code |
| `test` | Run unit tests |
| `package` | Package compiled code |
| `verify` | Perform verification checks |
| `install` | Store the artifact in the local repository |
| `deploy` | Publish the artifact to a remote repository |

Maven executes earlier lifecycle phases when you request a later phase. For example, `mvn package` also executes the earlier phases of that lifecycle.

**Note:** Maven's `deploy` phase normally publishes an artifact to a configured remote artifact repository. It does not automatically deploy an application to a production server.

### Run a simple Maven project

If the project has been configured with an appropriate executable JAR:

    java -jar target/my-java-app-1.0.0.jar

This works only when the JAR contains the necessary launch metadata and runtime dependencies are available or included as required.

A plain JAR containing classes may not be executable with `java -jar`.

---

## 6. Gradle Build Tool

Gradle is another build automation tool used for Java and many other languages.

Gradle supports build configuration using:
- Groovy DSL — `build.gradle`
- Kotlin DSL — `build.gradle.kts`

### Typical Gradle project structure

    my-java-app/
    ├── build.gradle
    ├── settings.gradle
    ├── gradlew
    ├── gradlew.bat
    └── src/
        ├── main/java/
        └── test/java/

The Gradle Wrapper (`gradlew`) allows the project to use its configured Gradle version without requiring a separate globally installed Gradle installation.

### Example `build.gradle`

    plugins {
        id 'java'
        id 'application'
    }

    group = 'com.example'
    version = '1.0.0'

    repositories {
        mavenCentral()
    }

    java {
        toolchain {
            languageVersion = JavaLanguageVersion.of(17)
        }
    }

    application {
        mainClass = 'com.example.App'
    }

### Important Gradle commands

Check version:

    ./gradlew --version

List tasks:

    ./gradlew tasks

Compile Java:

    ./gradlew compileJava

Run tests:

    ./gradlew test

Build the project:

    ./gradlew build

Create the JAR:

    ./gradlew jar

Clean generated files:

    ./gradlew clean

Clean and build:

    ./gradlew clean build

Run an application configured with the `application` plugin:

    ./gradlew run

The wrapper script may need execute permission:

    chmod +x gradlew

### Gradle build output

Common output directory:

    build/

A JAR may be generated under:

    build/libs/

The exact filename depends on the project name and version configuration.

### Maven vs Gradle

| Feature | Maven | Gradle |
|---|---|---|
| Configuration | XML (`pom.xml`) | Groovy or Kotlin DSL |
| Main build file | `pom.xml` | `build.gradle` or `build.gradle.kts` |
| Build output | Usually `target/` | Usually `build/` |
| Dependency management | Built in | Built in |
| Build execution | Lifecycle-based | Task graph |
| Wrapper | Maven Wrapper | Gradle Wrapper |
| Customization | Plugins and lifecycle configuration | Flexible tasks and plugins |

Both are widely used. Learn Maven first if your current course uses Maven, then learn Gradle concepts.

---

## 7. Apache Ant Build Tool

Apache Ant is a Java build automation tool that commonly uses an XML file named:

    build.xml

Ant organizes automation into targets and tasks.

### Example Ant project structure

    my-java-app/
    ├── build.xml
    └── src/
        └── Main.java

### Example `build.xml`

    <project name="MyJavaApp" default="jar" basedir=".">

        <property name="src.dir" value="src"/>
        <property name="build.dir" value="build"/>
        <property name="classes.dir" value="${build.dir}/classes"/>
        <property name="jar.dir" value="${build.dir}/libs"/>

        <target name="clean">
            <delete dir="${build.dir}"/>
        </target>

        <target name="compile">
            <mkdir dir="${classes.dir}"/>
            <javac srcdir="${src.dir}"
                   destdir="${classes.dir}"
                   includeantruntime="false"/>
        </target>

        <target name="jar" depends="compile">
            <mkdir dir="${jar.dir}"/>
            <jar destfile="${jar.dir}/myapp.jar"
                 basedir="${classes.dir}"/>
        </target>

    </project>

### Important Ant commands

Check version:

    ant -version

List available targets:

    ant -p

Compile:

    ant compile

Create a JAR:

    ant jar

Clean output:

    ant clean

Ant does not impose Maven's standard lifecycle and project layout. You define targets and dependencies explicitly in the build file.

---

## 8. What Is a JAR File?

JAR stands for Java Archive.

A JAR file packages Java class files, resources, and optional metadata into an archive based on the ZIP format.

Common uses:
- Java libraries.
- Command-line applications.
- Application packaging.
- Sharing compiled classes and resources.

### Create a basic JAR manually

Suppose `MyClass.java` has been compiled and `MyClass.class` exists.

    jar --create --file myapp.jar MyClass.class

List its contents:

    jar --list --file myapp.jar

A JAR can be used as a classpath dependency:

    java -cp myapp.jar MyClass

This example works when `MyClass` is in the default package and contains a suitable `main` method.

### Create an executable JAR

An executable JAR needs a manifest identifying its entry point.

Create a file named `manifest.txt`:

    Main-Class: MyClass

Include a blank line after the last manifest attribute.

Create the JAR:

    jar --create --file myapp.jar --manifest manifest.txt MyClass.class

Run it:

    java -jar myapp.jar

For Maven or Gradle applications, plugins can automate manifest generation and, when configured, package application dependencies.

**Important:** A standard JAR does not automatically include every external library needed at runtime.

---

## 9. What Is a WAR File?

WAR stands for Web Application Archive.

A WAR file packages a Java web application for deployment to a compatible servlet container or application server.

Typical contents include:

    myapp.war
    ├── index.jsp
    ├── css/
    ├── WEB-INF/
    │   ├── web.xml
    │   ├── classes/
    │   └── lib/
    └── ...

Common uses:
- Java servlet applications.
- JSP-based web applications.
- Web applications deployed to compatible containers, such as Tomcat.

### WAR deployment flow

    Java Web Application
             |
             v
        Maven / Gradle
             |
             v
         myapp.war
             |
             v
      Servlet Container
       (e.g. Tomcat)
             |
             v
       User accesses URL

### Maven WAR packaging

Set the packaging type in `pom.xml`:

    <packaging>war</packaging>

Build:

    mvn clean package

The WAR file is commonly generated in:

    target/

Deployment details depend on the container and application configuration.

### JAR vs WAR

| Feature | JAR | WAR |
|---|---|---|
| Full form | Java Archive | Web Application Archive |
| Common use | Libraries and Java applications | Java web applications |
| Contents | Classes, resources, metadata | Web resources, classes, libraries, metadata |
| Typical deployment | Java runtime or another application | Servlet container/application server |
| Common output | `app.jar` | `app.war` |

A JAR is not necessarily executable, and a WAR is not automatically compatible with every server. The package must match the application's runtime and deployment requirements.

---

## 10. How Build Tools Automate Software Delivery

Without automation, a developer may manually compile, test, package, and copy application files for every release.

A build tool makes these steps repeatable.

### Manual build process

    Developer writes code
             |
             v
        Run javac
             |
             v
       Run tests manually
             |
             v
      Package the artifact
             |
             v
        Copy to server
             |
             v
       Run the application

### Automated build process

    Developer pushes code to Git
                |
                v
       CI/CD pipeline starts
                |
                v
      Install/check JDK and tools
                |
                v
       Maven / Gradle / Ant
                |
                v
          Compile source
                |
                v
           Run tests
                |
                v
        Package JAR or WAR
                |
                v
      Publish build artifact
                |
                v
      Deploy to target environment
                |
                v
       Health checks and monitoring

The exact pipeline depends on the project. A pipeline should stop when required build or test stages fail.

### Why this matters for DevOps

- Repeatable builds.
- Automated testing.
- Consistent packaging.
- Faster releases.
- Reduced manual mistakes.
- Integration with Jenkins or GitHub Actions.
- Artifact publishing and controlled deployment.

---

## 11. Maven in a CI/CD Pipeline

Example Jenkins pipeline for a Maven Java project:

    pipeline {
        agent any

        stages {
            stage('Checkout') {
                steps {
                    checkout scm
                }
            }

            stage('Build and Test') {
                steps {
                    sh 'mvn clean verify'
                }
            }

            stage('Package') {
                steps {
                    sh 'mvn package'
                }
            }
        }
    }

This is a basic illustration. In a real pipeline, `mvn clean verify` already runs the earlier phases, including packaging, so the separate `Package` stage may be redundant. You can simplify it to one `Build and Test` stage and then publish the generated artifact.

Example:

    pipeline {
        agent any

        stages {
            stage('Checkout') {
                steps {
                    checkout scm
                }
            }

            stage('Build and Test') {
                steps {
                    sh 'mvn clean verify'
                }
            }

            stage('Archive Artifact') {
                steps {
                    archiveArtifacts artifacts: 'target/*.jar',
                                     fingerprint: true
                }
            }
        }
    }

This example assumes a JAR-producing Maven project and a Jenkins agent with Git, a compatible JDK, and Maven available. Adapt the artifact pattern for WAR projects.

---

## 12. Common Build Errors and Troubleshooting

### Error: `javac: command not found`

Possible cause:
- The JDK is not installed.
- The JDK `bin` directory is missing from `PATH`.

Check:

    java -version
    javac -version
    echo "$JAVA_HOME"
    echo "$PATH"

### Error: `Could not find or load main class`

Possible causes:
- Incorrect class name.
- Wrong working directory or classpath.
- Class is inside a package.
- Class file has not been compiled.

For a class in the default package:

    javac MyClass.java
    java MyClass

For a packaged class such as `com.example.App`:

    java com.example.App

Run it from the correct classpath root.

### Error: Maven build fails

Check:

    mvn -version
    mvn clean verify

Read the first meaningful compilation, dependency-resolution, or test error in the output.

Possible causes:
- Incorrect JDK version.
- Dependency repository is unavailable.
- Dependency version cannot be resolved.
- Compilation errors.
- Failed tests.
- Incorrect Maven configuration.

### Error: Gradle build fails

Check:

    ./gradlew --version
    ./gradlew clean build --stacktrace

Possible causes:
- Incompatible JDK and Gradle versions.
- Dependency download failure.
- Incorrect build configuration.
- Compilation or test failures.

### Error: Application JAR will not run

Check:

    jar --list --file app.jar
    java -jar app.jar

Possible causes:
- Missing or incorrect `Main-Class` manifest entry.
- Missing runtime dependencies.
- Unsupported Java version.
- Application configuration problems.

---

## 13. Essential Commands for Revision

| Task | Command |
|---|---|
| Check Java runtime | `java -version` |
| Check compiler | `javac -version` |
| Compile Java source | `javac MyClass.java` |
| Run a class | `java MyClass` |
| Generate Javadoc | `javadoc MyClass.java` |
| Inspect JAR contents | `jar --list --file app.jar` |
| Check Maven | `mvn -version` |
| Maven compile | `mvn compile` |
| Maven test | `mvn test` |
| Maven package | `mvn package` |
| Maven clean build | `mvn clean package` |
| Maven verification | `mvn clean verify` |
| Check Gradle | `./gradlew --version` |
| Gradle build | `./gradlew build` |
| Gradle clean build | `./gradlew clean build` |
| Check Ant | `ant -version` |
| Ant build target | `ant jar` |

## 14. Practical Hands-on Lab

### Part A: Manual Java build

- [ ] Create `MyClass.java`.
- [ ] Compile it using `javac MyClass.java`.
- [ ] Verify that `MyClass.class` exists.
- [ ] Run it using `java MyClass`.
- [ ] Generate documentation using `javadoc MyClass.java`.

### Part B: Maven build

- [ ] Create a Maven project.
- [ ] Understand the `pom.xml` file.
- [ ] Put source code under `src/main/java`.
- [ ] Run `mvn compile`.
- [ ] Run `mvn test`.
- [ ] Run `mvn clean package`.
- [ ] Inspect the artifact under `target/`.

### Part C: Understand packaging

- [ ] Inspect the contents of a JAR using `jar --list`.
- [ ] Understand the manifest and `Main-Class`.
- [ ] Understand when to package a web application as a WAR.
- [ ] Learn how a compatible application server runs a WAR.

### Part D: DevOps automation

- [ ] Push the Java project to GitHub.
- [ ] Configure Jenkins or GitHub Actions.
- [ ] Automate checkout, build, and tests.
- [ ] Archive or publish the generated artifact.
- [ ] Add deployment and health-check stages when ready.

---

## 15. Final Interview Revision

**What is a build tool?**

A build tool automates tasks such as compilation, dependency management, testing, packaging, and artifact generation.

**What is Maven?**

Maven is a Java build and dependency management tool configured primarily through `pom.xml`.

**What is Gradle?**

Gradle is a build automation tool that uses Groovy or Kotlin DSL and a task graph.

**What is Ant?**

Ant is a task-based Java build automation tool configured through XML.

**What is the difference between JDK and JVM?**

The JDK provides development tools and runtime components. The JVM executes Java bytecode.

**What is the difference between JAR and WAR?**

JAR packages Java classes and resources for libraries or applications. WAR packages Java web application resources for a compatible web container or application server.

**How do build tools help DevOps?**

They make builds repeatable and automate compilation, tests, artifact packaging, and integration with CI/CD pipelines.

**Remember the main flow:**

`Git → JDK → Maven/Gradle/Ant → Compile → Test → JAR/WAR → Artifact Repository → Deploy → Monitor`


