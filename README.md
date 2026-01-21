# Portainer Docker Swarm/Compose Setup

This project provides a Docker Compose and Swarm configuration for deploying Portainer Community Edition (CE) and its agent.

## Overview

This setup consists of two main services:

- **Portainer**: The main Portainer web interface for managing your Docker environment.
- **Agent**: The Portainer agent that runs on each node in the swarm to provide information to the Portainer instance.

This configuration is optimized for Docker Swarm, as indicated by the use of `deploy` keys, overlay networks, and global service mode for the agent.

## Prerequisites

- Docker and Docker Compose installed.
- A Docker Swarm cluster initialized (for swarm deployment).
- An external network named `traefik-public` must exist if you intend to use the Traefik labels.

## Configuration

1.  Create a `.env` file from the example:
    ```bash
    cp .env.example .env
    ```
2.  Edit the `.env` file and set the `PORTAINER_HOST` variable to the domain where you want to access Portainer.
    ```
    PORTAINER_HOST=portainer.your-domain.com
    ```

## Deployment

You can deploy this stack using either Docker Compose or Docker Swarm.

### Docker Swarm (Recommended)

```bash
docker stack deploy -c docker-compose.yml portainer
```

### Docker Compose

```bash
docker-compose up -d
```

## Traefik Integration

The `docker-compose.yml` file includes commented-out labels for integrating with a Traefik reverse proxy. If you are using Traefik, you can uncomment these labels to automatically expose Portainer with SSL.

Make sure the `traefik-public` network is correctly configured and that your Traefik instance is connected to it. You will also need a `cloudflare` cert resolver configured in your Traefik setup for the TLS to work as defined in the labels.

## Security

For security, the `cap_drop: - ALL` directive is used for both services to drop all capabilities, and only the necessary ones are added by Docker.

## Networks

- **agent_network**: An overlay network that allows the Portainer service to communicate with the agents.
- **traefik-public**: An external network for connecting to a Traefik reverse proxy.

## Volumes

- **portainer_data**: A named volume used to persist Portainer's data.
