## Introduction

To run Java programs from any location on your computer, Java must be configured in the system's **Environment Variables**.

Without proper configuration, commands like:

```
javajavac
```

may not work from the Command Prompt or Terminal.

The most important environment variable for Java is **PATH**.

---

# What are Environment Variables?

Environment Variables are system-wide settings that store information used by the operating system and applications.

They help the operating system locate programs and resources.

Examples:

```
PATHJAVA_HOMETEMPUSERNAME
```

---

# What is PATH?

## Definition

**PATH** is an environment variable that tells the operating system where executable programs are located.

When you type:

```
java
```

the operating system searches the folders listed in PATH.

---

# Purpose of PATH

Without PATH:

```
C:\Program Files\Java\jdk-21\bin\java
```

must be typed every time.

With PATH configured:

```
java
```

works from any folder.

---

# Example

### Without PATH

```
D:\Projects> java
```

Output:

```
'java' is not recognized as an internal or external command
```

---

### With PATH

```
D:\Projects> java --version
```

Output:

```
Java version information displayed
```

---

# Why PATH is Important?

PATH allows:

✅ Running Java from any directory

✅ Running javac from any directory

✅ Easy Java development

✅ IDE integration

---

# Java Installation Directory

Typical JDK Location in Windows:

```
C:\Program Files\Java\jdk-21
```

Inside it:

```
jdk-21 │ ├── bin ├── lib ├── include └── ...
```

Important executables are stored inside:

```
jdk-21\bin
```

Examples:

```
java.exejavac.exejavadoc.exejar.exejavap.exe
```

---

# Setting PATH Variable (Windows)

## Step 1

Locate JDK installation directory.

Example:

```
C:\Program Files\Java\jdk-21
```

---

## Step 2

Copy the bin path:

```
C:\Program Files\Java\jdk-21\bin
```

---

## Step 3

Open:

```
Control Panel     ↓System     ↓Advanced System Settings     ↓Environment Variables
```

---

## Step 4

Select:

```
Path
```

Click:

```
Edit
```

Add:

```
C:\Program Files\Java\jdk-21\bin
```

Save changes.

---

# JAVA_HOME Variable

## Definition

`JAVA_HOME` points to the JDK installation directory.

Example:

```
JAVA_HOME=C:\Program Files\Java\jdk-21
```

---

# Why JAVA_HOME is Used?

Many tools use JAVA_HOME:

- Apache Maven
- Apache Tomcat
- Gradle
- Jenkins
- Spring Boot Tools

---

# Relationship Between JAVA_HOME and PATH

Example:

```
JAVA_HOME=C:\Program Files\Java\jdk-21
```

PATH:

```
%JAVA_HOME%\bin
```

This is preferred because if Java version changes, only JAVA_HOME needs updating.

---

# Checking Java Installation

## Check Java Runtime

Command:

```
java --version
```

Example Output:

```
java 21.0.2Java(TM) SE Runtime EnvironmentJava HotSpot(TM) 64-Bit Server VM
```

---

# What Does It Verify?

Checks whether:

- Java is installed
- PATH is configured correctly
- JVM is available

---

# Check Java Compiler

Command:

```
javac --version
```

Example Output:

```
javac 21.0.2
```

---

# What Does It Verify?

Checks whether:

- JDK is installed
- Compiler is available
- PATH is configured correctly

---

# Difference Between java and javac

|Command|Purpose|
|---|---|
|java|Runs Java programs|
|javac|Compiles Java programs|

---

# Example

## Compile

```
javac Main.java
```

Produces:

```
Main.class
```

---

## Run

```
java Main
```

Produces:

```
Hello Java
```

---

# Complete Flow

```
Write Code(Main.java)       │       ▼Compile(javac Main.java)       │       ▼Main.class       │       ▼Run(java Main)       │       ▼Output
```

---

# Common Errors

## Error 1: Java Not Recognized

Command:

```
java --version
```

Output:

```
'java' is not recognized as an internal or external command
```

### Reason

PATH is not configured correctly.

---

## Error 2: javac Not Recognized

Command:

```
javac --version
```

Output:

```
'javac' is not recognized as an internal or external command
```

### Reason

JDK is not installed or PATH is incorrect.

---

## Error 3: Only JRE Installed

Command:

```
java --version
```

Works ✅

Command:

```
javac --version
```

Fails ❌

### Reason

Compiler is available only in JDK.

---

# Interview Questions

### Q1. What is PATH?

**Answer:**  
PATH is an environment variable that allows executable programs such as `java` and `javac` to run from any directory.

---

### Q2. Why is PATH required for Java?

**Answer:**  
It allows the operating system to locate Java executables without specifying their full path.

---

### Q3. How do you check whether Java is installed?

```
java --version
```

---

### Q4. How do you check whether the Java compiler is installed?

```
javac --version
```

---

### Q5. What is JAVA_HOME?

**Answer:**  
JAVA_HOME is an environment variable that stores the JDK installation directory.

Example:

```
JAVA_HOME=C:\Program Files\Java\jdk-21
```

---

### Q6. Which directory should be added to PATH?

**Answer:**

```
<JDK Installation Directory>\bin
```

Example:

```
C:\Program Files\Java\jdk-21\bin
```

---

# Quick Revision

```
Environment Variables        │        ▼      PATH        │        ▼Allows java and javac commandsto run from any folder
```

### Commands

```
java --version
```

Check Java Runtime

```
javac --version
```

Check Java Compiler

---

# Summary

- Environment Variables help the operating system locate programs.
- PATH allows `java` and `javac` commands to run from any directory.
- `JAVA_HOME` stores the JDK installation path.
- `java --version` checks Java runtime installation.
- `javac --version` checks compiler installation.
- Proper PATH configuration is essential for Java development.
- Questions about PATH, JAVA_HOME, `java`, and `javac` are very common in Java fresher interviews.