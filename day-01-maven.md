## Part 1 – Why Maven Exists

Before Maven, building a Java project meant doing many things manually.

A Java project needs answers to very basic questions:

- How do we compile Java source files?
- Where do external libraries (JARs) come from?
- In what order should steps like compile, test, and package happen?
- How do we ensure the same build works on every machine?

If every developer answers these questions differently:
- builds break
- projects behave inconsistently
- onboarding becomes difficult

**Maven exists to standardize these answers.**

So the real purpose of Maven is:

> To provide a consistent and repeatable way to build Java projects.

Maven does not solve business problems.  
It solves **build and consistency problems**.


## Part 2 – What Maven Really Is

Maven is **not a compiler** and **not magic**.

The correct understanding is:

- Maven is **just a Java program**
- It runs on the JVM using the `java` command
- Maven itself does **not** compile Java code
- Maven does **not** run tests by itself

Instead, Maven’s real job is coordination.

Maven:
- reads project information from `pom.xml`
- decides what needs to happen
- decides the correct order
- delegates actual work to other tools

So the accurate statement is:

> Maven is a **build coordinator**, not a compiler.

Maven does not do the work.  
It **decides who should do the work and when**.

## Part 3 – Relationship Between OS, javac, and Maven

To understand Maven clearly, we must separate **who runs whom**.

Key facts:

- `javac` is just an **OS-level command**
- The operating system runs `javac` like any other executable
- Maven is another program that runs on the JVM
- Maven does **not replace** `javac`

The actual execution flow is:
Operating System
↓
mvn command
↓
Java (JVM)
↓
Maven program
↓
javac


What Maven really does here:

- It decides **which source files** should be compiled
- It decides **which JARs** form the classpath
- It decides **which flags** to pass
- Then it asks the OS to run `javac` with those inputs

So the important realization is:

>Maven does not compile code itself;  
>it decides **how** `javac` should be invoked.

## Part 4 – Why Maven Is Called a Build Tool

Maven is called a **build tool** not because it compiles code,  
but because it **controls the entire build process**.

A build is not just compilation.

A real build involves decisions like:

- What source files should be compiled?
- Which external libraries are needed?
- In what order should steps run?
- When should tests run?
- When should a final artifact be created?

Maven answers all of these questions **before** any tool is executed.

What Maven actually decides:

- **WHAT** should be built (project structure)
- **WHERE** the source code is located
- **WHICH** dependencies form the classpath
- **HOW** tools like `javac` should be invoked
- **WHEN** each step should run (order)

After deciding all this, Maven tells the OS:

> “Run this tool with these inputs.”

So the correct understanding is:

> Maven is a **build tool because it plans and controls the build**,  
> not because it performs compilation itself.

## Part 5 – What `pom.xml` Really Is

`pom.xml` is **not code** and **not logic**.

The correct understanding is:

- `pom.xml` is **pure data**
- It contains **no execution instructions**
- It cannot do anything by itself
- Only Maven reads and understands it

Maven treats `pom.xml` as an **input file**.

Maven reads data from `pom.xml` to answer questions like:
- What is this project?
- What dependencies does it need?
- How should the build be performed?
- Which tools should be used?

So the correct mental model is:

> `pom.xml` is an **answer sheet** for Maven.

Nothing inside `pom.xml` runs automatically.  
Everything happens only because **Maven interprets this data**.

## Part 6 – Maven Project Identity (groupId, artifactId, version)

Before Maven can build anything, it must know **what this project is**.

So Maven requires three mandatory identity values:

```xml
<groupId>
<artifactId>
<version>
```

These answer:
- Who am I?
- What is my project name?
- Which version am I?

Without these values, Maven refuses to run.


## Part 7 – Parent POM (How Maven Handles Inheritance)

First, an important clarification:

- **XML itself has NO inheritance**
- Any inheritance behavior you see is implemented by **Maven**, not XML

---

### Why Maven Needs a Parent POM

Maven noticed a common problem:

- Many projects repeat the same configuration
- Same plugin versions
- Same dependency versions
- Same default settings

To avoid repetition, Maven allows one POM to **reuse configuration from another**.

That reused POM is called the **parent POM**.

---

### What Actually Happens When Maven Sees `<parent>`

When Maven encounters this:

```xml
<parent>
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
</parent>
```

Maven does the following:

1. Reads the parent POM as data  
2. Reads the child POM as data  
3. Merges both into one **effective POM**

Important clarifications:
- No constructor is called
- No code is executed
- No runtime behavior happens

It is **only data merging**, and **child overrides parent**.


## Part 8 – Dependencies (What They Actually Mean)

Dependencies are one of the most misunderstood parts of Maven.

The correct understanding is very simple:

> Dependencies exist **only to build a classpath**.

---

### What Problem Dependencies Solve

When Java code is compiled, `javac` needs to know:
- Where required classes are located
- Which external JAR files contain those classes

Without this information:
- Compilation fails
- Classes cannot be found

Dependencies exist to solve **this exact problem**.

---

### What Maven Does with Dependencies

When Maven sees dependencies in `pom.xml`:

- It downloads the required JAR files
- It stores them in the local repository (`.m2`)
- It collects their file paths
- It builds a **classpath list**

Maven then passes this classpath to `javac`.

Important:
- Maven does NOT execute dependency code
- Maven does NOT inspect business logic
- Maven only manages **JAR files and paths**

---

### What a Dependency Is NOT

A dependency is NOT:
- a running service
- executable logic
- something Maven uses directly

A dependency IS:
- a JAR file
- placed on the classpath
- so `javac` and the JVM can find required classes

---

### Dependency Scope (Why It Exists)

`scope` controls **when** a dependency is available.

Common scopes:
- `compile` → available everywhere
- `test` → available only during testing
- `runtime` → available only when running

Scope does NOT change code behavior.  
It only changes **when the JAR appears on the classpath**.

---

### Correct Mental Model

> Dependencies are just **classpath inputs**, nothing more.


## Part 9 – Plugins (Who Actually Does the Work)

Maven by itself **cannot do real work**.

Important reality:
- Maven cannot compile Java code
- Maven cannot run tests
- Maven cannot create JAR files

So Maven needs **helpers**.

These helpers are called **plugins**.

---

### What a Plugin Really Is

A Maven plugin is:

- a **Java program**
- already compiled and packaged as a JAR
- downloaded by Maven like any other dependency

A plugin contains:
- Java classes
- logic to perform a specific task
- logic to invoke tools like `javac`

---

### Why Plugins Exist

Maven’s job is coordination, not execution.

So Maven:
- decides **what should happen**
- decides **when it should happen**
- delegates **how it happens** to plugins

Example:
- Compilation → compiler plugin
- Testing → test plugin
- Packaging → jar plugin

---

### Relationship Between Maven and Plugins

Correct mental model:

- Maven = manager / coordinator
- Plugin = worker

Maven says:
> “It is time to compile.”

Plugin says:
> “I know how to compile. I will do it.”

---

### Important Clarification

Plugins:
- do NOT run automatically
- do NOT execute by themselves
- run only when Maven calls them during a build step

So the correct understanding is:

> Plugins do the actual work; Maven only decides **when** to use them.

## Part 10 – Build Section (How Tools Are Invoked)

The `<build>` section exists to answer **one specific question**:

> “How should Maven invoke the tools that do the work?”

It does **not** define business logic.  
It does **not** change application behavior.

---

### What Problem the Build Section Solves

Maven already knows:
- what the project is
- where source code is
- which dependencies exist
- which lifecycle phase is running

Now Maven must decide:
- which plugin should run
- what configuration that plugin should use
- what arguments should be passed to tools like `javac`

That information is provided in the `<build>` section.

---

### What the Build Section Contains

The build section mainly contains:

- plugins to be used
- configuration for those plugins

Example idea (conceptual, not full config):

- compiler plugin
- Java version
- output directories

---

### What Maven Does with the Build Section

When a lifecycle phase is reached:

1. Maven checks which plugin is responsible
2. Maven reads that plugin’s configuration from `<build>`
3. Maven prepares tool arguments
4. Maven asks the plugin to execute

Maven itself still does **no real work**.

---

### Important Clarification

The `<build>` section:
- controls **how tools are called**
- does NOT control application logic
- does NOT add features to your program

Correct understanding:

> The build section is a **tool-invocation configuration**, nothing more.

## Part 11 – Properties (Why They Exist and What They Really Do)

Properties exist to solve a **very simple problem**:

> Repeating the same value in many places causes mistakes.

---

### What Problem Properties Solve

Without properties:
- The same value (like Java version) is written multiple times
- Changing it requires editing many places
- This easily leads to inconsistency

Properties allow **one value to be defined once and reused**.

---

### What a Property Really Is

A Maven property is:

- a **named text value**
- used for **replacement**
- resolved **before** the build starts

Example:

```xml
<java.version>21</java.version>
```

## Part 12 – Maven Lifecycle (The Fixed Order of Steps)

The Maven lifecycle is often misunderstood, but it is actually **very simple**.

Correct understanding:

> Maven lifecycle is a **fixed, predefined order of steps**.

It is not dynamic.  
It does not change per project.

---

### Why Maven Needs a Lifecycle

A build is not just one action.

A real build involves multiple steps, such as:
- checking the project
- compiling code
- running tests
- packaging the output

If these steps run in random order, builds become unreliable.

So Maven defines **one standard order** and follows it strictly.

---

### The Standard Maven Lifecycle Order

validate → compile → test → package → verify → install → deploy


This order is **fixed** and **cannot be changed**.

---

### The Most Important Rule

> If you ask Maven to run a step,  
> **all steps before it will run automatically**.

Examples:
- `mvn compile` → `validate` + `compile`
- `mvn package` → `validate` + `compile` + `test` + `package`
- `mvn install` → runs everything up to `install`

---

### What the Lifecycle Does and Does NOT Do

Lifecycle:
- decides **order**
- decides **when** something should happen

Lifecycle does NOT:
- compile code itself
- run tests itself
- create JARs itself

Actual work is always done by **plugins**.

---

### Correct Mental Model

> Lifecycle = order  
> Plugins = workers

The lifecycle only says **when**.  
Plugins decide **how**.





