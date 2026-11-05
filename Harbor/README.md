# Harbor Container Registry

## 1. Project Overview

Harbor is an open-source container registry used to store, manage, scan, and distribute Docker images securely.

This project demonstrates the installation and configuration of Harbor on an Ubuntu server using Docker Compose.

## 2. Project Details

| Configuration | Value |
|---|---|
| Project Name | Harbor Container Registry |
| Registry | Harbor |
| Operating System | Ubuntu Linux |
| Container Runtime | Docker |
| Deployment | Docker Compose |
| Harbor Version | v2.15.4 |
| Hostname | harbor.local |
| HTTP Port | 86 |
| Access URL | http://harbor.local:86 |

## 3. Prerequisites

Before installing Harbor, ensure that the following are installed:

- Ubuntu Linux
- Docker Engine
- Docker Compose
- Minimum 4 GB RAM (8 GB recommended)
- Sufficient disk space
- Sudo privileges

## 4. Install Required Packages

Update the Ubuntu package list:

```bash
sudo apt update
```

Install Docker and required utilities:

```bash
sudo apt install -y docker.io docker-compose-v2 curl wget tar
```

Enable and start Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verify Docker:

```bash
docker --version
docker compose version
```

## 5. Configure Harbor Hostname

Edit the hosts file:

```bash
sudo nano /etc/hosts
```

Add:

```text
127.0.0.1 harbor.local
```

Save and exit.

If Harbor runs on another server, replace `127.0.0.1` with that server's IP address.

## 6. Configure Harbor Port

Edit the Harbor configuration file:

```bash
sudo nano /opt/harbor/harbor.yml
```

Set the following configuration:

```yaml
hostname: harbor.local

http:
  port: 86

data_volume: /data
```

Configure a strong administrator password using the `harbor_admin_password` setting in the same file.

Keep the existing HTTPS configuration consistent with your deployment requirements.

## 7. Install Harbor

Go to the Harbor installation directory:

```bash
cd /opt/harbor
```

Run the installer:

```bash
sudo ./install.sh
```

Wait until the installation completes.

## 8. Start and Check Harbor

Check running containers:

```bash
sudo docker compose ps
```

View container logs:

```bash
sudo docker compose logs
```

Check the port:

```bash
sudo ss -lntp | grep 86
```

## 9. Access Harbor Web UI

Open your browser:

**URL:** http://harbor.local:86

Default username:

```text
admin
```

Password:

Use the administrator password configured in `harbor.yml`.

## 10. Login to Harbor Using Docker

```bash
docker login harbor.local:86
```

Enter your Harbor username and password.

If Docker rejects HTTP because the registry is not using HTTPS, configure the Docker daemon's insecure registry setting for `harbor.local:86` only in your local practice environment, then restart Docker.

## 11. Upload a Docker Image

Pull an example image:

```bash
docker pull nginx:latest
```

Tag the image:

```bash
docker tag nginx:latest harbor.local:86/library/nginx:latest
```

Push the image:

```bash
docker push harbor.local:86/library/nginx:latest
```

## 12. Verify Uploaded Image

1. Open Harbor Web UI.
2. Log in as administrator.
3. Open the `library` project.
4. Select the `nginx` repository.
5. Verify the uploaded image tag.

## 13. Useful Commands

| Purpose | Command |
|---|---|
| Check Docker | `sudo systemctl status docker` |
| Check Harbor containers | `sudo docker compose ps` |
| View Harbor logs | `sudo docker compose logs` |
| Stop Harbor | `sudo docker compose down` |
| Start Harbor | `sudo docker compose up -d` |
| Check port 86 | `sudo ss -lntp \| grep 86` |

Run Docker Compose commands from the directory containing Harbor's generated `docker-compose.yml`.

## 14. Troubleshooting

### Harbor UI is not opening

```bash
sudo docker compose ps
sudo docker compose logs
```

### Port 86 is already in use

```bash
sudo ss -lntp | grep 86
```

### Docker login fails

Check the registry hostname, port, Docker daemon configuration, and Harbor logs.

### Harbor containers are not running

```bash
cd /opt/harbor
sudo docker compose up -d
```

## 15. Security Notes

- Use strong administrator passwords.
- Do not publish passwords or private credentials.
- Enable HTTPS for production environments.
- Restrict registry access using firewall rules.
- Back up Harbor data regularly.
- Do not expose an HTTP registry to untrusted networks.

## 16. Conclusion

Harbor has been installed and configured as a private container registry on Ubuntu.

The registry can be accessed using:

**http://harbor.local:86**

Docker images can be pushed, stored, managed, and pulled through the Harbor registry.

---

**Project:** Harbor Container Registry  
**Platform:** Ubuntu Linux  
**Container Runtime:** Docker  
**Deployment:** Docker Compose  
