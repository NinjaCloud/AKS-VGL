# Lab 6: Docker Networking

This lab demonstrates how to work with Docker networking by creating bridges, testing container connectivity, and experimenting with various network types such as `host` and `none`.

---

## Prerequisites
1. Docker installed on your system.
2. Basic knowledge of Docker commands.

---

## Task 1: Create a New Docker Bridge and Check Connectivity Between Containers in the Same Bridge

### Steps:
1. **List existing Docker networks**:
   ```bash
   docker network ls
   ```
   Observe the default networks: `bridge`, `host`, and `none`.

2. **Create a new bridge network**:
   ```bash
   docker network create --driver bridge ct-bridge1
   ```

3. **Inspect the newly created bridge**:
   ```bash
   docker network inspect ct-bridge1
   ```

4. **Verify the new network**:
   ```bash
   docker network ls
   ```

5. **Run the first container on the new bridge**:
   ```bash
   docker run -it --network ct-bridge1 --name=ct-c1 busybox
   ```
   Inside the container, you can interact with the shell. Use `Ctrl+P+Q` to detach from the container without stopping it.

6. **Run a second container on the same bridge**:
   ```bash
   docker run -it --network ct-bridge1 --name=ct-c2 busybox
   ```
   Detach using `Ctrl+P+Q`.

7. **Inspect the bridge to confirm container connectivity**:
   ```bash
   docker network inspect ct-bridge1
   ```

8. **List running containers**:
   ```bash
   docker ps
   ```

9. **Attach to the second container and test connectivity**:
   ```bash
   docker attach ct-c2
   ip addr
   ping -c 5 ct-c1
   ```
   Use `Ctrl+P+Q` to exit back to the host.

### Explanation:
Containers connected to the same bridge can communicate with each other using their container names as hostnames. This is a key feature of Docker's bridge network driver.

---

## Task 2: Create a New Docker Bridge and Check Connectivity Between Containers on Different Bridges

### Steps:
1. **Create a second bridge network**:
   ```bash
   docker network create --driver bridge ct-bridge2
   ```

2. **Run containers on the second bridge**:
   - Run the third container:
     ```bash
     docker run -it --network ct-bridge2 --name=ct-c3 busybox
     ```
     Detach using `Ctrl+P+Q`.
   - Run the fourth container:
     ```bash
     docker run -it --network ct-bridge2 --name=ct-c4 busybox
     ```
     Detach using `Ctrl+P+Q`.

3. **Attach to the fourth container and test connectivity**:
   ```bash
   docker attach ct-c4
   ping -c 5 ct-c3
   ip addr
   ```

4. **Test cross-bridge connectivity**:
   - Attempt to ping containers on the first bridge (`ct-c1` and `ct-c2`) from `ct-c4`:
     ```bash
     ping -c 5 ct-c1
     ping -c 5 ct-c2
     ```
   Use `Ctrl+P+Q` to exit back to the host.

### Explanation:
Containers on separate bridges are isolated and cannot communicate unless explicitly connected.

---

## Task 3: Connect Containers from Different Bridges

### Steps:
1. **List existing networks**:
   ```bash
   docker network ls
   ```

2. **Connect a container from the first bridge to the second bridge**:
   ```bash
   docker network connect ct-bridge2 ct-c1
   ```

3. **Inspect the second bridge**:
   ```bash
   docker network inspect ct-bridge2
   ```

4. **Attach to the connected container and test communication**:
   ```bash
   docker attach ct-c1
   ping -c 5 ct-c4
   ip addr
   ip route
   ```
   Detach using `Ctrl+P+Q`.

### Explanation:
The `docker network connect` command allows you to connect a container to multiple networks, enabling communication across bridges.

---

## Task 4: Launch a Container on the Host Network

### Steps:
1. **Run a container on the host network**:
   ```bash
   docker run -it --network host --name=ct-c5 busybox
   ```
   Inside the container, run:
   ```bash
   ip addr
   ifconfig
   ```
   Detach using `Ctrl+P+Q`.

2. **Inspect the host network**:
   ```bash
   docker network inspect host
   ```

### Explanation:
When a container uses the `host` network, it shares the host machine's network namespace. This means the container can directly access host services but loses network isolation.

---

## Task 5: Launch a Container on the None Network

### Steps:
1. **Run a container with the `none` network**:
   ```bash
   docker run -it --network none --name=ct-c6 busybox
   ```
   Inside the container, run:
   ```bash
   ip addr
   ```
   Detach using `Ctrl+P+Q`.

### Explanation:
The `none` network disables networking for the container. This is useful for testing or when the container does not require network access.

---

