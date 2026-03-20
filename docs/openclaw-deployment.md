# 🌐 Scalable Agent Infrastructure (OpenClaw + Proxmox)

This document details the deployment of a professional multi-tenant environment for AI agents, tailored for an engineering team of 30+ users.

## 🏗️ Architecture Overview
To ensure data security and performance, I transitioned from a single-user setup to a containerized multi-tenant architecture.

1. **Virtualization Layer (Proxmox):**
   - Agents run in isolated **Ubuntu 24.04 LTS LXC containers**.
   - Custom configurations for `/dev/net/tun` to enable secure networking for Tailscale.
2. **Secure Connectivity (Tailscale & SSH):**
   - Remote instances connect to the local **RTX 5090 inference server** via a Tailscale mesh VPN.
   - Admin access is secured through **SSH Port Forwarding** (Port 18789), bypassing browser security barriers for local-only WebSockets.
3. **Multi-Tenant Docker Setup:**
   - Every employee receives an isolated Docker container with a unique **Telegram Bot Token** and a dedicated `workspace` folder.
   - Individual identities and system prompts are defined via `IDENTITY.md` files.

## 📂 Engineering Data Pipeline (Samba/SMB)
I integrated a **Samba (SMB) server** into the stack to create a seamless bridge between Windows workstations and the Linux/Docker environment.
* **Workflow:** Engineers map their agent's workspace as a network drive in Windows. 
* **Action:** Users "Drag & Drop" complex technical PDF/DOCX files into the drive.
* **Analysis:** The local AI agent instantly processes the file using the **RTX 5090**, ensuring proprietary engineering data never leaves the internal network.

---
*Back to [Main README](../README.md)*
