---
title: "Week 1: Starting My DevOps Journey - Ubuntu Server on VMware"
date: 2025-11-20
draft: false
tags: ["homelab", "ubuntu", "vmware", "docker", "portainer", "devops"]
categories: ["DevOps Journey"]
---

## The Beginning

I've decided to dive into DevOps, cloud technologies, and backend engineering. While I'm waiting for my physical server to arrive( an old beat up system unit), I'm not wasting any time I've set up Ubuntu Server on VMware Fusion to start building my homelab skills.

## What I've Accomplished So Far

### Setting Up Ubuntu Server on VMware Fusion

Getting Ubuntu Server running in a VM was my first step. The goal was simple: create an environment where I could experiment, break things, and learn without consequences.

**Key steps:**
- Downloaded Ubuntu Server ISO
- Created a new VM in VMware Fusion
- Configured network settings for SSH access
- Completed the installation

### SSH Access - My First Win

One of the most satisfying moments was successfully SSH-ing into my Ubuntu Server from my Mac terminal. No more switching to the VMware window - just clean terminal access.

```bash
ssh username@vm-ip-address
```

This might seem basic, but it's the foundation of how you'll interact with servers in the real world. No GUI, just command line.

### Installing Docker

Docker is essential for modern DevOps, so it was my first major installation:

```bash
# Downloaded and ran Docker installation script
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Added my user to the docker group
sudo usermod -aG docker $USER
```

After logging out and back in, I could run Docker commands without sudo - a small quality of life improvement that matters.

### Portainer - My Container Management Dashboard

Rather than managing everything purely through CLI (though I will be doing that too), I installed Portainer to visualize what's happening with my containers:

```bash
docker volume create portainer_data

docker run -d \
  -p 9000:9000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

Now I can access a web interface at `http://my-vm-ip:9000` or for https `https://my-vm-ip:9443` and see all my containers, images, volumes, and networks at a glance.

## Why This Approach?

I deliberately chose **plain Ubuntu Server** over user-friendly options like CasaOS because:

1. **Real world relevance**: This is what I'll work with professionally
2. **No abstractions**: I need to understand what's happening under the hood
3. **Transferable skills**: Everything I learn applies to cloud environments (AWS, Azure, GCP)
4. **Problem solving**: When things break, I'll learn to fix them

## What's Next?

My plan for the coming weeks:

- **Week 2**: Deploy my first multi-container application with docker compose
- **Week 3**: Set up monitoring with Prometheus and Grafana (If I can figure it out)
- **Week 4**: Install and explore Kubernetes (probably k3s)
- **Ongoing**: Document everything, break things, learn from failures

## Lessons Learned

**Start simple.** I was tempted to jump straight into Kubernetes, but understanding Docker fundamentals first is crucial. You can't orchestrate containers if you don't understand how containers work.

**Documentation matters.** That's why I'm writing this blog. Future me (and maybe others) will benefit from these notes.

**VMs are perfect for learning.** I can snapshot, experiment, break things, and restore without consequences. When my physical server arrives, I'll already know what I'm doing.

## Current Setup

- **Host**: MacBook Pro 2016  running VMware Fusion
- **VM**: Ubuntu Server (latest LTS)
- **Installed**: Docker, Portainer
- **Access**: SSH from terminal
- **Status**: Ready to build

The journey has just begun. Next week, I'll be deploying actual applications and diving deeper into Docker networking and volumes or just breaking things LOL.

---

*This is part of my weekly DevOps learning series. Follow along as I document my journey from beginner to proficient.*