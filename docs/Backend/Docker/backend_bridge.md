---
sidebar_position: 1
---

# Creating the Bridge Network

To set up the bridge network for your Docker containers, follow these steps:

## 1. Create a Bridge Network for Staging

Run the following command to create a new bridge network:

```bash
docker network create docker_network_crowdfund
```

## 2. List All Docker Networks

To verify the networks available, list them using this command:

```bash
docker network ls
```

## 3. Verify the Network Exists

Check that your newly created network, `docker_network_crowdfund`, is in the list of available networks.
