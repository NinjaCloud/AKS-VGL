# Lab: Creating a Virtual Machine on Azure Cloud with Ubuntu 22.04 OS and Standard B2s Machine Type

This lab provides a step-by-step guide to create an Azure virtual machine (VM) with Ubuntu 22.04 OS using the Standard B2s machine type. Additionally, it includes creating a new resource group named `docker`.

---

## Prerequisites
1. An active Azure subscription.
2. Access to the Azure Portal ([https://portal.azure.com](https://portal.azure.com)).
3. Basic knowledge of cloud concepts and Azure.

---

## Step 1: Log in to Azure Portal
1. Open your web browser and navigate to [Azure Portal](https://portal.azure.com).
2. Sign in with your Azure credentials.

---

## Step 2: Create a Resource Group
1. In the Azure Portal, search for **"Resource groups"** in the top search bar and click on it.
2. Click the **"+ Create"** button.
3. Fill in the following details:
   - **Subscription**: Select your Azure subscription.
   - **Resource Group Name**: Enter `docker`.
   - **Region**: Choose a region close to you or your users (e.g., East US, West Europe).
4. Click **Review + Create**, and then **Create**.

---

## Step 3: Create a Virtual Machine
1. Search for **"Virtual machines"** in the Azure Portal search bar and click on it.
2. Click the **"+ Create"** button and select **Virtual machine**.

---

## Step 4: Configure Basics
1. Fill in the following details in the **Basics** tab:
   - **Subscription**: Select your subscription.
   - **Resource Group**: Select the resource group `docker` created in Step 2.
   - **Virtual Machine Name**: Enter a unique name for your VM (e.g., `ubuntu-vm`).
   - **Region**: Choose the same region as the resource group.
   - **Availability Options**: Select **No infrastructure redundancy required**.
   - **Image**: Select **Ubuntu Server 22.04 LTS - Gen2**.
   - **Size**: Click **Change size** and select **Standard B2s** (2 vCPUs, 4 GiB memory).
2. Configure the **Administrator Account**:
   - **Authentication Type**: Select **Password**.
   - **Username**: Enter a username (e.g., `azureuser`).
   - **Password**: Enter and confirm a strong password.
3. For **Inbound Port Rules**:
   - Select **Allow selected ports**.
   - Check the box for **SSH (22)**.
4. Click **Next: Disks**.

---

## Step 5: Configure Disks
1. For the OS Disk type, select **Standard SSD** or another option based on your requirements.
2. Leave the other settings as default and click **Next: Networking**.

---

## Step 6: Configure Networking
1. Review the default Virtual Network (VNet) and Subnet settings. Leave them as default unless you need specific configurations.
2. Public IP: Ensure that a Public IP is assigned.
3. Leave the other settings as default and click **Next: Management**.

---

## Step 7: Configure Management
1. **Monitoring**: You can enable or disable monitoring features as needed:
   - **Boot diagnostics**: Enable.
   - **OS guest diagnostics**: Optional.
2. Leave other settings as default and click **Next: Advanced**.

---

## Step 8: Advanced Settings
1. Leave the advanced settings as default unless specific customizations are required.
2. Click **Next: Tags**.

---

## Step 9: Add Tags
1. (Optional) Add tags for better resource management. For example:
   - **Name**: `Environment`
   - **Value**: `Dev`
2. Click **Next: Review + Create**.

---

## Step 10: Review and Create
1. Review all the configurations and ensure they meet your requirements.
2. Click **Create**. Azure will validate the configuration and start deploying the VM.

---

## Step 11: Access the Virtual Machine
1. Once deployment is complete, go to the **Virtual machines** section.
2. Click on your VM name (e.g., `ubuntu-vm`).
3. Note the **Public IP Address** from the VM overview.
4. Open an SSH client (e.g., PuTTY, terminal) and connect to the VM:
   ```bash
   ssh azureuser@<Public-IP-Address>
