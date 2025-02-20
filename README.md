# Ghidorah-Portainer

This repository contains the Docker Compose configurations for deploying and managing Portainer on my server, "Ghidorah".

## Overview

This repository is specifically designed for managing Portainer instances using Docker Compose on the server named "Ghidorah".  Portainer is a lightweight management UI which allows you to easily manage your different Docker environments (Docker hosts or Swarm clusters).

## Prerequisites

Before deploying, ensure the following prerequisites are met on the Ghidorah server:

*   **Docker Engine:** Docker must be installed and running.  Verify with `docker --version` and `docker info`.
*   **Docker Compose:** Docker Compose must be installed.  Verify with `docker-compose --version`.  (Note: If using the newer Docker plugin system, you may have `docker compose version` instead).
*   **Network Access:** The server must have network access (specifically, be able to pull Docker images from Docker Hub or a private registry if you are using one).  The appropriate ports (see below) should be open in the firewall.
*  **User Permissions:** The user deploying Portainer should have the necessary permissions to run Docker commands.  Generally, this means being part of the `docker` group.
*  **DNS (Optional but Recommended):** A DNS record pointing to the server's IP address is helpful for accessing Portainer easily (e.g., `portainer.yourdomain.com`).

## Directory Structure

The repository's directory structure may contain multiple compose files for different Portainer configurations.  A common, basic structure would be:

*   `README.md`: This file, providing an overview and instructions.
*   `docker-compose.yml`:  The primary Docker Compose file for deploying a standard Portainer instance.
* `docker-compose-agent.yml` (Optional): A Docker Compose file for deploying a Portainer Agent.  This is useful for managing a remote Docker host or Swarm cluster.
* `docker-compose-edge.yml` (Optional): A compose file for deploying a Portainer Edge agent.
*   `volumes/` (Optional):  A directory to hold persistent data, if managed outside the compose file directly (this is less common and not recommended for beginners).

## Deployment Instructions

1.  **SSH into Ghidorah:** Access your server (Ghidorah) via SSH.
2.  **Clone the Repository:**
    ```bash
    git clone [https://github.com/YOUR_USERNAME/ghidorah-portainer.git](https://www.google.com/search?q=https://github.com/YOUR_USERNAME/ghidorah-portainer.git)
    cd ghidorah-portainer
    ```
    Replace `YOUR_USERNAME` with your actual GitHub username.

3.  **Choose a Compose File:**  Select the appropriate `docker-compose.yml` file (or its variant) based on your needs (e.g., standard instance, agent, edge agent).

4.  **Deploy Portainer:**
    Use `docker-compose` (or `docker compose`) to deploy the chosen configuration.  From the directory containing the `docker-compose.yml` file, run:

    ```bash
    docker-compose up -d
    ```
    (Or `docker compose up -d` if using the Docker plugin system.)
    The `-d` flag runs the containers in detached mode (in the background).

5.  **Initial Portainer Setup (First Time Only):**

    *   **Access Portainer:** After the deployment is complete, open a web browser and navigate to `http://<ghidorah_ip_address>:9000` or `https://<ghidorah_ip_address>:9443` (replace `<ghidorah_ip_address>` with the actual IP address or domain name of your server).  You may need to wait a few minutes for Portainer to fully initialize. If you're using a DNS name, use that instead of the IP address.  *The default Portainer UI port is 9000 (HTTP) or 9443 (HTTPS).*  If you've changed the ports in your compose file, use those instead.
    *   **Create Admin User:** Follow the on-screen instructions to create the initial administrator user.
    *   **Connect to Docker Environment:** Choose how to connect Portainer to your Docker environment.  For a local setup (as in this case), select "Docker" to manage the local Docker Engine.

6.  **Update (Optional):**
    To update Portainer to a newer version:

    ```bash
    docker-compose pull
    docker-compose up -d
    ```
    (Or the equivalent `docker compose` commands.)

7. Stop (Optional):
    To stop and remove the container:
     ```bash
    docker-compose down
    ```
    (Or the equivalent `docker compose` commands.)

## Configuration (docker-compose.yml)

The `docker-compose.yml` file contains the configuration for Portainer. Here's a breakdown of a typical example, along with explanations of common options:

```yaml
version: '3.8'

services:
  portainer:
    image: portainer/portainer-ce:latest  # Use the latest Community Edition image
    container_name: portainer
    restart: always  # Restart the container if it fails
    ports:
      - "8000:8000"  # Port for Edge Agent HTTP Tunnel (if using Edge Agent)
      - "9443:9443"  # HTTPS Port for Portainer UI (Recommended)
      - "9000:9000" # HTTP Port (Less Secure, use only if you know what you're doing)
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock  # Mount the Docker socket (REQUIRED)
      - portainer_data:/data # Persistent data volume (HIGHLY RECOMMENDED)
    networks:
      - portainer_network #Optional

volumes:
  portainer_data: #Define the volume.

networks:
  portainer_network: #Optional
