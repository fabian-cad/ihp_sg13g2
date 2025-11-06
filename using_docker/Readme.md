# IHP 130 nm Open PDF on Ubuntu 24.04 Docker Container
**Autor:** Fabián Olivera
**Date:** November 2025

## 1. Installing Docker

### Installing docker (on Linux):
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-rocky-linux-8

### Installing docker (on Windows):
Just install Docker Desktop application from: https://www.docker.com/

## 2. Creating docker image
Inside the folder that contains 'Dockerfile' (current Readme.md folder), execute the following command to build the image:
```bash
docker build -t cad_ihp130_ubuntu_image:latest .
```

## 3. Running container from the created docker image
Then, iniciate the container as:
```bash
docker run --privileged --name cad_ihp130_ubuntu_container -d -p <open_port_host>:22 --restart unless-stopped --hostname cad01 cad_ihp130_ubuntu_image:latest
```

## 4. Connecting to the container via SSH with X11 forwarding
Access to the container using SSH (you need to open <open_port_host> port of the host) by 
```bash
ssh -YC caduser@<hostname> -p <open_port_host>
```
The users “caduser” and “root” share the same password: cad1234. The user “caduser” can execute sudo commands without a password.