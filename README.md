# Assetto Corsa Evo (Proton) Pterodactyl Egg

Custom Pterodactyl Egg for the Assetto Corsa Evo dedicated server running natively via Proton.

## Prerequisites
* A Pterodactyl Panel instance.
* Docker installed on your host node.

## Host Node Preparation
To bypass Pterodactyl read-only restrictions and allow the dashboard to write to configuration files, you MUST build a local Docker wrapper before installing this server. Run these commands on your host node via SSH:

```bash
mkdir -p ~/acevo-build && cd ~/acevo-build
# (Paste your Dockerfile content here or use the heredoc method)
cat <<EOF> Dockerfile
FROM zino1337/acevo-server:latest
USER root
RUN rm -rf /data && ln -s /home/container /data
RUN rm -rf /root && ln -s /home/container /root
USER 1000
EOF
docker build -t acevo-ptero:latest .
