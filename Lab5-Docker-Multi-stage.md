# Lab: Multi-Stage Build in Docker (Java Application)

This lab demonstrates how to use Docker multi-stage builds with a simple Java application. Multi-stage builds allow you to separate the build and runtime environments, reducing the final image size and improving security.

---

## Prerequisites
1. Docker installed on your system.
2. Basic knowledge of Dockerfile syntax and commands.
3. Java Development Kit (JDK) installed locally to test the application (optional).

---

## Objective
Create a Docker image for a Java application using a multi-stage build to separate the build process from the runtime environment.

---

## Task: Create a Multi-Stage Build Dockerfile for a Java Application

### Step 1: Set Up the Project Directory
1. Create a new directory for the Java project:
   ```bash
   mkdir java-multi-stage
   cd java-multi-stage
   ```

2. Create a simple Java application:
   ```bash
   vi Main.java
   ```

3. Add the following Java code to the file:
   ```java
   public class Main {
       public static void main(String[] args) {
           System.out.println("Hello, Docker Multi-Stage Build with Java!");
       }
   }
   ```

---

### Step 2: Create the Multi-Stage Dockerfile
1. Create a Dockerfile in the same directory:
   ```bash
   vi Dockerfile
   ```

2. Add the following content to the Dockerfile:
   ```dockerfile
   # Stage 1: Build
   FROM openjdk:17-jdk-slim AS builder
   WORKDIR /app
   COPY Main.java .
   RUN javac Main.java

   # Stage 2: Runtime
   FROM openjdk:17-jre-slim
   WORKDIR /app
   COPY --from=builder /app/Main.class .
   CMD ["java", "Main"]
   ```

---

### Step 3: Build the Docker Image
1. Build the Docker image using the Dockerfile:
   ```bash
   docker build -t java-multi-stage-example .
   ```

2. Verify that the image is created:
   ```bash
   docker image ls
   ```

---

### Step 4: Run the Container
1. Start a container from the built image:
   ```bash
   docker run --rm java-multi-stage-example
   ```

2. Observe the output:
   ```
   Hello, Docker Multi-Stage Build with Java!
   ```

---

## Explanation of Key Steps
1. **Stage 1: Build Stage**
   - Uses the `openjdk:17-jdk-slim` image to compile the Java application.  
   - The `javac` command compiles the `Main.java` file into a `Main.class` bytecode file.

2. **Stage 2: Runtime Stage**
   - Uses the lightweight `openjdk:17-jre-slim` image, which only includes the Java Runtime Environment (JRE).
   - The compiled bytecode file (`Main.class`) from the build stage is copied to this stage using the `COPY --from=builder` command.
   - The final image does not include the Java compiler or source code, minimizing its size.

3. **Advantages of Multi-Stage Builds**
   - Reduces the size of the final Docker image by excluding unnecessary build tools.
   - Improves security by only including runtime dependencies.
   - Simplifies the build and deployment process for Java applications.

---

## Optional: Use Python Instead of Java
For a Python example, replace `Main.java` with a Python script and skip the compilation step in the build stage. However, Python applications typically don't require multi-stage builds because they don't need a separate compilation step.

---
