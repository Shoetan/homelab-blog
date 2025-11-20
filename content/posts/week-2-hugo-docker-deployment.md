---
title: "Week 2: Deploying My Hugo Blog with Docker and Nginx"
date: 2025-11-29
draft: false
tags: ["hugo", "docker", "nginx", "git", "deployment", "homelab"]
categories: ["DevOps Journey"]
---

## From Localhost to Production

Last week I set up Ubuntu Server on VMware Fusion and installed Docker and Portainer. This week, I took it further by building an actual blog with Hugo and deploying it using Docker and Nginx. You're reading the result of that work right now!

## Why Hugo?

I needed a way to document my learning journey, and Hugo checked all the boxes:

- **Static site generator** - No database, no server-side processing
- **Markdown-based** - I can write in plain text and version control everything
- **Fast** - Builds sites in milliseconds
- **Themes available** - I used PaperMod theme out of the box
- **Perfect for learning** - Deployment teaches me Docker, Nginx, and Git workflows

## Setting Up Hugo Locally

### Installation on Mac

```bash
brew install hugo
hugo version
```

### Creating the Site

```bash
hugo new site devops-blog
cd devops-blog
git init
```

### Adding a Theme

I went with PaperMod - clean, minimalist, and perfect for a technical blog:

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### Configuration

The `hugo.toml` file controls everything. Key settings I configured:

```toml
baseURL = 'http://192.168.198.130/'
title = 'My DevOps Journey'
theme = 'PaperMod'
```

The `baseURL` is critical it needs to match where you're hosting the blog, otherwise navigation breaks (learned that the hard way!).

## Git Workflow

I pushed everything to GitHub for version control:

```bash
git add .
git commit -m "Initial Hugo setup"
git remote add origin https://github.com/username/devops-blog.git
git push -u origin main
```

**Why Git matters here:**
- My content is version controlled
- I can write on my Mac and deploy to my VM
- If I break something, I can roll back
- Professional workflow practice

## Deploying to Ubuntu Server

### The Architecture

Here's what I built:

1. **Hugo** generates static HTML/CSS/JS files into a `public/` folder
2. **Nginx** (running in Docker) serves those files
3. **Docker volume** mounts the `public/` folder into the container
4. **Port 80** exposed so I can access via browser

### Step-by-Step Deployment

**On my VM:**

```bash
# Clone the repository
cd ~/projects
git clone https://github.com/username/homelab-blog.git
cd homelab-blog

# Initialize theme submodule
git submodule update --init --recursive

# Install Hugo
sudo snap install hugo

# Build the site
hugo
```

This created the `public/` folder with my entire static site.

### Nginx with Docker

Instead of installing Nginx directly on the system, I used Docker:

```bash
docker run -d \
  --name blog \
  -p 80:80 \
  -v ~/projects/homelab-blog/public:/usr/share/nginx/html:ro \
  --restart=always \
  nginx:alpine
```

**Breaking down this command:**
- `-d` - Runs in background (detached)
- `--name blog` - Names the container for easy reference
- `-p 80:80` - Maps port 80 on VM to port 80 in container
- `-v ~/projects/homelab-blog/public:/usr/share/nginx/html:ro` - Mounts my Hugo files into Nginx
- `:ro` - Read-only (container can't modify my files)
- `--restart=always` - Auto-restart on crashes or reboots
- `nginx:alpine` - Lightweight Alpine Linux version

### Verification

```bash
# Check container is running
docker ps

# Test locally
curl localhost

# Check from Mac
# Visited http://192.168.198.130 in browser
```

Success! My blog was live.

## The Update Workflow

When I write new posts, here's my process:

**On Mac:**
```bash
# Create new post
hugo new posts/my-new-post.md

# Write content, preview locally
hugo server -D

# Commit and push
git add .
git commit -m "Add new blog post"
git push origin main
```

**On VM:**
```bash
# Pull changes
cd ~/projects/homelab-blog
git pull origin main

# Rebuild
hugo
```

The Docker container automatically serves the updated files - no restart needed!

### Automation Script

I created a deployment script to make updates easier:

```bash
#!/bin/bash
cd ~/projects/homelab-blog
echo "Pulling latest changes..."
git pull origin main
echo "Building site..."
hugo
echo "Blog updated successfully!"
```

Saved as `~/scripts/update-blog.sh` and made executable with `chmod +x`.

## Challenges and Solutions

### Problem 1: Navigation Not Working

Initially, my homepage loaded but clicking any link gave 404 errors.

**Cause:** My `baseURL` was set to `https://192.168.198.130/` but I was serving over `http://`.

**Solution:** Changed `baseURL` to `http://192.168.198.130/` and rebuilt. Hugo generates all internal links based on this setting.

### Problem 2: Theme Not Loading

The PaperMod theme wasn't appearing after cloning on the VM.

**Cause:** Git submodules aren't cloned automatically.

**Solution:** Ran `git submodule update --init --recursive` to pull the theme.

### Problem 3: Accessing from Other Devices

My blog worked on my Mac but not on my phone.

**Cause:** VMware's NAT networking creates an isolated network (`192.168.198.x`). My phone was on my home WiFi (`192.168.1.x`).

**Understanding:** For local network access, I'd need to switch to Bridged networking. For internet access, I'd need port forwarding or cloud hosting (future project when physical server arrives).

## What I Learned

**Docker volumes are powerful:** I can update files on the host and the container immediately serves the new content. No rebuilding the image needed.

**Static sites are simple to deploy:** No database, no PHP, no complex configuration. Just HTML files and a web server.

**Git submodules require attention:** They don't clone automatically and need explicit initialization.

**Configuration matters:** One wrong URL in `baseURL` breaks everything. Details matter in DevOps.

**Container naming helps:** Using `--name blog` makes management easier than Docker's random names like "romantic_tesla".

## Current Setup

- **Local development:** MacBook Pro 2016 with Hugo
- **Version control:** GitHub repository
- **Production:** Ubuntu Server VM
- **Web server:** Nginx in Docker (Alpine)
- **Deployment:** Git pull + Hugo build
- **Access:** `http://192.168.198.130`

## What's Next

For Week 3, I'm planning to:

- Deploy my first multi-container application with docker-compose
- Maybe a simple Node.js API + MongoDB stack
- Set up Prometheus and Grafana for monitoring
- Start exploring Docker networking in depth

The blog is now running, containerized, and version-controlled. Every week I document here will be deployed through the same workflow I just built.

---

*This is part of my weekly DevOps learning series. The blog you're reading was deployed using the exact process described above.*