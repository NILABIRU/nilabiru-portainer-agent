# Nilabiru Portainer Agent

A lightweight Docker Compose setup that runs the Portainer Agent, enabling remote Docker environment management from a central Portainer CE instance in the Nilabiru ecosystem.

---

## Overview

**Nilabiru Portainer Agent** deploys a single [Portainer Agent](https://docs.portainer.io/admin/environments/add/docker/agent) container on a remote Docker host. Once running, the agent exposes port `9001` so that a central Portainer CE instance (such as the one running in `nilabiru-data-hub`) can connect to and manage this host's containers, images, volumes, and networks from a single web UI.

---

## Services

| Service                      | Image                    | Port   | Description                                                                          |
| ---------------------------- | ------------------------ | ------ | ------------------------------------------------------------------------------------ |
| **nilabiru-portainer-agent** | `portainer/agent:2.42.0` | `9001` | Portainer Agent that exposes the local Docker environment to a Portainer CE instance |

---

## Requirements

- Docker Engine `20.10+`
- Docker Compose `v2+`
- Port `9001` reachable from the host running Portainer CE (either via Tailscale, VPN, or direct network access)
- A running Portainer CE instance to connect to this agent

---

## Getting Started

### 1. Fast Deploy (Recommended for Multi-VM)

If you want to install this agent on many new VMs without cloning the repository, you can directly execute the `docker-compose.yml` file *remotely* using the following command in the target VM's terminal:

```bash
curl -s https://raw.githubusercontent.com/andry-pebrianto/nilabiru-data-hub-agent/main/docker-compose.yml | docker compose -f - up -d
```

### 2. Standard Deploy (Manual Clone)

If you want to store the configuration file locally on the VM:

```bash
git clone https://github.com/andry-pebrianto/nilabiru-data-hub-agent.git
cd nilabiru-data-hub-agent
docker compose up -d
```

To make sure the agent container is running properly, use the following command:

```bash
docker compose ps
```

---

## 3. Connect from Portainer CE

In your Portainer CE main dashboard:

1. Go to **Environments → Add environment**.
2. Select **Docker Standalone** → **Agent**.
3. Enter an identity name for this new VM and fill in the **Agent URL** with `<HOST_IP>:9001`.
4. Click **Add environment**.

> **Note:** Replace `<HOST_IP>` with the IP address or hostname of the machine where the agent is installed (for example, using a Tailscale IP for better security).

---

## Data Persistence

This service does not store any persistent data on its own. The container only performs *bind mounts* on two host paths with *read-write* access so that Portainer CE can monitor and manage them:

| Mount                     | Type       | Purpose                             |
| ------------------------- | ---------- | ----------------------------------- |
| `/var/run/docker.sock`    | Bind mount | Docker socket access for API calls  |
| `/var/lib/docker/volumes` | Bind mount | Volume browsing via Portainer CE UI |

---

## License

This project is licensed under the [MIT License](LICENSE).  
Copyright © 2026 Andry Pebrianto