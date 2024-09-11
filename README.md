# Grafana Deployment with Docker, Traefik, and GitLab CI/CD

This repository contains the setup and CI/CD pipeline for deploying Grafana using Docker and Traefik. The deployment process utilises GitLab CI/CD for continuous delivery, SSH, and rsync for file transfer, and Docker Compose for container management.

## Prerequisites

Ensure that the following are set up before deploying:

1. **Docker & Docker Compose**: Installed on the remote server.
2. **SSH Access**: SSH keys must be configured for connecting to the remote server.
3. **Traefik**: Already running as the reverse proxy.
4. **Environment Variables**: Sensitive variables like Grafana admin password and SMTP credentials should be configured in GitLab CI/CD.

### Required GitLab CI/CD Environment Variables

- `SSH_PRIVATE_KEY`: SSH private key for the deployment user.
- `SSH_DOCKER_USER`: SSH user for remote access.
- `REMOTE_IPADDRESS`: IP address of the remote server.
- `GF_SECURITY_ADMIN_PASSWORD`: Grafana admin password.
- `GF_USERS_ALLOW_SIGN_UP`: Allows or disallows signups in Grafana.
- `GF_SMTP_USER`: SMTP user for sending emails.
- `GF_SMTP_PASSWORD`: SMTP password for sending emails.
- `GF_SMTP_FROM_ADDRESS`: Email address for Grafana to send emails from.
  
## Pipeline Overview

This project uses a GitLab CI/CD pipeline for deploying the Grafana service to a remote server. The pipeline is composed of the following stages:

1. **Test**: Verifies SSH connectivity to the remote server.
2. **Deploy**: Deploys the Docker Compose setup to the remote server.
3. **Shutdown**: Stops the running Docker containers.
4. **Destroy**: Removes the Docker containers and volumes.
5. **Cleanup**: Cleans up the remote server deployment directory.

### Deployment Workflow

1. **SSH and rsync**: The deployment process uses `rsync` to copy project files to the remote server.
2. **Docker Compose**: The deployment job uses `docker-compose` to manage the Grafana service and related containers on the remote server.
3. **SSH and Cleanup**: After deployment, the deployment directory on the remote server is cleaned up.

## Key Components

### Volumes:

- grafana_data: Stores Grafana data (dashboards, logs, etc.).
- grafana_provisioning: Contains provisioning configuration for Grafana.
- grafana_plugins: Stores plugins for Grafana.

### Networks:

- The Grafana service is connected to the traefik network for reverse proxying.

### Environment Variables:

- Environment variables for Grafana configuration are passed through the .env file. These include settings for the admin password, user signup, SMTP configuration, and root URL.

### Healthcheck:

- Grafana is monitored with a health check that sends a request to /api/health.

### Logging:

- Logs are sent to Fluentd at 127.0.0.1:24224 with the tag loki.grafana.

## Traefik Labels

The service is configured with Traefik labels to enable reverse proxying:

- Router Rule: Routes traffic for the domain grafana-docker-1.jodywan.com.
- Entrypoints: Allows HTTP traffic through the web entry point.
- Load Balancer: The service listens on port 3000.

## Usage

### Running the Pipeline

To deploy Grafana, run the CI/CD pipeline manually from GitLab. The pipeline consists of several manual jobs, including deployment, stopping services, and destroying containers.

###  Accessing Grafana
Once deployed, Grafana will be accessible at https://grafana-docker-1.domain.com. Use the credentials defined in the environment variables to log in.

### Customising .env

It is **not recommended** to store sensitive information such as passwords directly in the `.env` file and push it to the repository, as this can expose critical data. Instead, sensitive values such as passwords should be stored in GitLab CI/CD environment variables for secure management.

The `.env` file can still be used for non-sensitive configuration settings. For example:

```env
# Non-sensitive configurations can be kept here
COMPOSE_PROJECT_NAME=grafana
GF_USERS_ALLOW_SIGN_UP=false
```

For sensitive configurations like passwords, SMTP credentials, and Grafana admin settings, store these in GitLab CI/CD variables, these GitLab CI/CD variables will automatically be injected into the pipeline during the deployment process, ensuring security and preventing the exposure of sensitive information in your codebase.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🇬🇧🇭🇰 Author Information

* Author: Jody WAN
* Linkedin: https://www.linkedin.com/in/jodywan/
* Website: https://www.jodywan.com