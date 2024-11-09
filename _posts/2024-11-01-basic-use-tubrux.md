---
layout: post
---

# Table of Contents
- [Minimum Requirements](#minimum-requirements)
- [Installation](#installation)
  - [Installing Tubrux with Maven](#installing-tubrux-with-maven)
  - [Installing Tubrux with Gradle](#installing-tubrux-with-gradle)
- [Using Tubrux in Your Java or Kotlin Project](#using-tubrux-in-your-java-or-kotlin-project)
  - [Creating an Instance](#creating-an-instance)
- [Configuring Tubrux](#configuring-tubrux)
- [Running the Analysis](#running-the-analysis)
  - [Example Output](#example-output)
- [Conclusion](#conclusion)

## How to Install and Use Tubrux for Analyzing Code Vulnerabilities

Tubrux is a powerful Java library designed to help developers analyze vulnerabilities in their code, specifically focusing on thread-safety issues and other potential security flaws in Java and Kotlin applications. This article will guide you through the process of installing and using Tubrux in your project.

### Minimum Requirements
- **Java 8** or higher

### Installation

#### Installing Tubrux with Maven

1. Add the following dependency to your `pom.xml` file:

```xml
<repositories>
    <repository>
        <id>tubrux-repo</id>
        <url>https://repo.repsy.io/mvn/hangga/repo</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>io.tubrux</groupId>
        <artifactId>tubrux</artifactId>
        <version>0.0.2-SNAPSHOT</version>
    </dependency>
</dependencies>
```

2. After adding the dependency, Maven will automatically download and include Tubrux in your project.

#### Installing Tubrux with Gradle

1. Add the following to your `build.gradle` file:

```groovy
repositories {
    maven { url 'https://repo.repsy.io/mvn/hangga/repo' }
}

dependencies {
    implementation 'io.tubrux:tubrux:0.0.2-SNAPSHOT'
}
```

2. Gradle will handle the installation and integrate Tubrux into your project.

### Using Tubrux in Your Java or Kotlin Project

#### Creating an Instance

To start using Tubrux, you need to create an instance of the main class where the analysis will be performed.

In **Java**, the following example demonstrates how to create an instance of the main class:

```java
public class Main {
    public static void main(String[] args) {
        new Tubrux()
            .setShowDate(true)
            .setDetectSensitiveData(true)
            .scan();
    }
}
```

In **Kotlin**, the process is similar:

```java
fun main() {
    Tubrux()
        .setShowDate(true)
        .setDetectSensitiveData(true)
        .scan()
}
```

### Configuring Tubrux

Tubrux offers several configuration options to customize the analysis based on your needs. You can configure these options before running the analysis.

The following options are available for configuration:

| **Configuration Option**         | **Description**                                                                                         | **Default Value** |
|-----------------------------------|---------------------------------------------------------------------------------------------------------|-------------------|
| `setShowDate(boolean value)`      | Whether or not to display the date in the output. Set to `false` to disable.                           | `false`            |
| `setDetectDeadlock(boolean value)`| Whether or not to detect deadlocks during analysis. Set to `false` to disable deadlock detection.       | `false`            |
| `setDetectSensitiveData(boolean value)` | Whether or not to detect sensitive data (e.g., passwords, tokens) in the code. Set to `false` to disable. | `false`            |
| `setIgnoreCommentBlock(boolean value)`  | Whether or not to include comment blocks in the analysis. Set to `true` to ignore comment blocks.     | `false`           |


### Running the Analysis

Once you have created an instance of `TubruxAnalyzer` and set the configurations, you can perform the analysis on your codebase. The analysis will check for vulnerabilities related to thread safety, deadlocks, sensitive data exposure, and more.

#### Example Output

The output from the analysis will show detailed reports about the potential vulnerabilities detected in your code. Here’s an example of what the output might look like:

<img src="https://github.com/tubrux/blog/blob/dark/_posts/example-output.png?raw=true"/>

### Conclusion

Tubrux is a valuable tool for any Java or Kotlin developer looking to ensure the security and thread-safety of their code. With easy installation via Maven or Gradle, flexible configuration options, and detailed vulnerability analysis reports, Tubrux can help you identify and mitigate potential risks in your codebase effectively.
