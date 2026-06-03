# IHP 130 nm on Ubuntu 24.04 Docker Container

**Author:** Professor Fabián Olivera, COPPE/UFRJ  
**Date:** June 3, 2026

---

## 1. Install Docker

Install Docker according to your operating system and distribution.

Official Docker documentation:

https://docs.docker.com/engine/install/

---

## 2. Build the Docker Image

Navigate to the directory containing the `Dockerfile` and execute:

```bash
docker build -t cad_ihp130_ubuntu_image:latest .
```

This command builds the Docker image and assigns it the tag
`cad_ihp130_ubuntu_image:latest`.

---

## 3. Start a Container

Create and start a container from the previously built image:

```bash
docker run --privileged --name cad_ihp130_container --hostname <hostname> -d -p <host_port>:22 --restart unless-stopped cad_ihp130_ubuntu_image:latest
```

Replace `<host_port>` with the port number that will be used for SSH access to the container.
Replace `<hostname>` with a hostname for the container.

---

## 4. Connect to the Container via SSH with X11 Forwarding

Connect to the container using SSH with X11 forwarding enabled:

```bash
ssh -YC <user>@<hostname> -p <host_port>
```

### Example

```bash
ssh -YC student01@localhost -p 5022
```

### Notes

- Ensure that the selected `<host_port>` is open in the host firewall, if applicable.
- The users `<user>` and `root` share the same password, which is defined in the `Dockerfile` before the image is built.
- The `-Y` option enables trusted X11 forwarding.
- The `-C` option enables SSH compression, which may improve performance when forwarding graphical applications. 
- Use `localhost` as the hostname when the SSH client is running on the same host as the Docker

---

## Summary

1. Install Docker.
2. Build the image using `docker build`.
3. Start the container using `docker run`.
4. Connect through SSH with X11 forwarding enabled.
