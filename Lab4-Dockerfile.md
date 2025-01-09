# Lab : Building a Dockerfile to Set Up an Ubuntu Container with WordPress Application

This lab provides a step-by-step guide to building a Dockerfile for an Ubuntu container, configuring it to host a WordPress application, and deploying it alongside a MySQL container.

---

## Prerequisites
1. Docker installed on your system.
2. Basic knowledge of Dockerfile syntax and commands.

---

## Task 1: Deploying MySQL and WordPress Containers

### Steps:

### Step 1: Create a Directory for the WordPress Project
1. Create a new directory to store your Dockerfile:
   ```bash
   mkdir wordpress
   cd wordpress
   ```

2. Create a Dockerfile using a text editor:
   ```bash
   vi Dockerfile
   ```

3. Paste the following content into the Dockerfile:
   ```dockerfile
   FROM ubuntu:18.04
   MAINTAINER ADMIN "admin@cloudthat.com"
   ENV DEBIAN_FRONTEND=noninteractive
   RUN apt-get update && \
       apt-get -q -y install apache2 \
       php7.2 \
       php7.2-fpm \
       php7.2-mysql \
       libapache2-mod-php7.2
   ADD http://wordpress.org/latest.tar.gz /tmp
   RUN tar xzvf /tmp/latest.tar.gz -C /tmp && \
       cp -R /tmp/wordpress/* /var/www/html
   RUN rm /var/www/html/index.html && \
       chown -R www-data:www-data /var/www/html
   EXPOSE 80
   CMD ["/bin/bash","-c","service apache2 start && sleep 5000"]
   ```

   Save and exit the editor.

> **Tip**: Alternatively, download the Dockerfile using the following command:
   ```bash
   wget https://hpe-content.s3.ap-south-1.amazonaws.com/Dockerfile
   ```

---

### Step 2: Build the WordPress Docker Image
1. Build the Docker image from the Dockerfile:
   ```bash
   docker build -t ct-wordpress:v1 .
   ```

2. Verify the created image:
   ```bash
   docker image ls
   ```

---

### Step 3: Create a Docker Network
1. Create a custom Docker bridge network:
   ```bash
   docker network create --driver bridge ct-bridge
   ```

---

### Step 4: Deploy the MySQL Container
1. Run a MySQL container connected to the `ct-bridge` network:
   ```bash
   docker run -d --network ct-bridge --name mysql \
       -e MYSQL_DATABASE=wordpress \
       -e MYSQL_USER=admin \
       -e MYSQL_PASSWORD=password \
       -e MYSQL_ROOT_PASSWORD=password \
       mysql:5.7
   ```

2. Verify the running container:
   ```bash
   docker ps
   ```

---

### Step 5: Deploy the WordPress Container
1. Run the WordPress container, exposing it on port 80:
   ```bash
   docker run -d --network ct-bridge -p 80:80 ct-wordpress:v1
   ```

2. Verify the running containers:
   ```bash
   docker ps
   ```

---

### Step 6: Test the Deployment
1. Open a web browser and navigate to the server's IP address or hostname on port 80:
   ```
   http://<server-ip>
   ```

2. You should see the WordPress setup page. Follow the on-screen instructions to complete the installation.

---

## Explanation of Key Steps
- **Dockerfile**: Builds an Ubuntu-based container with Apache, PHP, and WordPress installed.
- **Custom Network**: The `ct-bridge` network allows MySQL and WordPress containers to communicate securely.
- **MySQL Environment Variables**: Configure the MySQL container with database details required by WordPress.
- **Port Mapping**: The WordPress container's port 80 is mapped to the host's port 80, making the application accessible.

---

