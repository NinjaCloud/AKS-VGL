```markdown
# Lab: Installing Docker on an Azure Virtual Machine (Ubuntu 22.04)

This lab provides a step-by-step guide to install Docker on an Azure Virtual Machine running Ubuntu 22.04 and enables Docker commands to be run without root access.

---

## Prerequisites
1. An Azure Virtual Machine (VM) with Ubuntu 22.04 OS created (Refer to the previous lab).
2. SSH access to the VM with the username and password configured during creation.

---

## Step 1: Connect to the Virtual Machine
1. Open a terminal or SSH client (e.g., PuTTY).
2. Connect to the VM using the following command:
   ```bash
   ssh azureuser@<Public-IP-Address>
   ```
   Replace `<Public-IP-Address>` with the public IP address of your VM.

3. Enter the password for the `azureuser` account when prompted.

---

## Step 2: Switch to Root User
To ensure you have the necessary privileges to install Docker, switch to the root user:
```bash
sudo su
```

---

## Step 3: Update the System
Run the following command to update the package lists:
```bash
apt update -y
```

---

## Step 4: Install Required Tools
Install the `curl` utility, which is required to download the Docker installation script:
```bash
apt install curl -y
```

---

## Step 5: Install Docker
Run the Docker installation script:
```bash
curl -SSL https://get.docker.com/ | sh
```

---

## Step 6: Verify Docker Installation
1. Check if the Docker service is running:
   ```bash
   service docker status
   ```
   The output should show that the Docker service is **active**.

2. Verify the Docker version to confirm successful installation:
   ```bash
   docker --version
   ```

---

## Step 7: Allow Non-Root Users to Run Docker Commands
By default, Docker requires root privileges to execute commands. To enable a non-root user (e.g., `azureuser`) to run Docker commands, perform the following steps:

1. Add the user to the `docker` group:
   ```bash
   usermod -aG docker azureuser
   ```

2. Apply the changes by logging out and back in:
   ```bash
   exit
   ssh azureuser@<Public-IP-Address>
   ```

3. Test Docker without `sudo`:
   ```bash
   docker run hello-world
   ```

   If the setup is correct, the command will pull the `hello-world` image and display a welcome message.

---

## Conclusion
You have successfully installed Docker on your Azure VM running Ubuntu 22.04 and enabled non-root users to run Docker commands. Your VM is now ready for containerized application deployment and development.
```
