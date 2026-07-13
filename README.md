# Assetto Corsa Evo (Proton) Pterodactyl Egg

This repository provides a custom Pterodactyl Egg and Docker wrapper instructions to run an Assetto Corsa Evo dedicated server natively via Proton.

## Why this Egg is different

Standard Assetto Corsa Evo server images are not designed for Pterodactyl’s restrictive filesystem. To make this work, we use a custom Docker wrapper that allows the container to properly manage persistent storage and provides a web-based dashboard for easy in-game configuration.

## Prerequisites

* A running Pterodactyl Panel instance.
* SSH access to your Pterodactyl Host Node (the server running the Docker daemon).
* Docker installed on Host

## Step 1: Host Node Preparation

Pterodactyl mounts volumes in a way that can cause configuration files to reset. We must build a local Docker image that creates the necessary symlinks to keep your data persistent.

1. SSH into your Host Node.
2. Run the following commands to build the wrapper:

```bash
# Create the build directory
mkdir -p ~/acevo-build && cd ~/acevo-build

# Create the Dockerfile
cat <<EOF> Dockerfile
FROM zino1337/acevo-server:latest
USER root
RUN rm -rf /data && ln -s /home/container /data
RUN rm -rf /root && ln -s /home/container /root
USER 1000
EOF

# Build the image
docker build -t acevo-ptero:latest .
```

## Step 2: Import the Egg

1. Download the `egg-assetto-corsa-evo.json` file from this repository to your local computer.
2. Log into your Pterodactyl Admin Panel.
3. Navigate to **Nests**.
4. Click on the Nest where you want to add this server.
5. Click **Import Egg** and upload the `egg-assetto-corsa-evo.json` file.

## Step 3: Create the Server

1. Create a new server in Pterodactyl using the Assetto Corsa Evo (Proton) Egg.
2. In the **Startup** tab, fill in the required variables (Steam Username/Password, Ports).
    * *Note: It is highly recommended to use a dedicated "burner" Steam account for this to protect your personal account credentials.*
3. Start the server and wait for the files to download.

## Step 4: Configuration & Usage

This server is designed to be managed via its built-in Web Dashboard, not by editing Pterodactyl variables.

### ⚠️ IMPORTANT: Managing Settings

* Do NOT add extra variables to the Pterodactyl panel for Server Name, Password, or Rules.
* The server's startup script monitors these variables. If you change them in the panel, they will overwrite your Web Dashboard settings every time the server restarts.
* Always use the Web Dashboard UI to save your server name, track, and car settings.

### Accessing the Web Dashboard

* The dashboard runs internally on port `8090`.
* Because this port is internal, you must route traffic to it using a Reverse Proxy (like NPMplus) on your network.
* Point your internal domain (e.g., `ac-server.lan`) to the container's internal IP on port `8090`.

## Security Disclaimers

* **Permissions:** This Egg utilizes a custom Docker build that elevates to root briefly to create storage symlinks, then immediately drops privileges to USER 1000 (non-root) before the container finishes building. This ensures the server runs in an unprivileged user-space for your security.
* **Credentials:** Pterodactyl environment variables are stored in plaintext. Use a dedicated Steam account ("burner account") that has no personal payment or sensitive information linked to it.
