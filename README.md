# CipherNex-301  | Space Security CTF Play Box

[![Platform](https://img.shields.io/badge/Platform-Ubuntu%20Linux%20Windows-orange.svg)](https://ubuntu.com/)
[![Docker](https://img.shields.io/badge/Container-Docker%20CE-blue.svg)](https://www.docker.com/)
[![Reverse%20Proxy](https://img.shields.io/badge/Web%20Server-Nginx-green.svg)](https://nginx.org/)
[![Backend](https://img.shields.io/badge/API-Flask%20%2F%20Python%203-blue.svg)](https://flask.palletsprojects.com/)

**CipherNex-301** is an 8-stage interactive Capture The Flag (CTF) security challenge platform that simulates the unexpected reactivation of a decommissioned experimental satellite acting as a covert Command & Control (C2) relay.

Players progress through a realistic space-security narrative while solving challenges spanning OSINT, network forensics, memory forensics, cryptography, steganography, Linux privilege escalation, and hybrid network-crypto analysis.

---

## Table of Contents

- [Overview](#overview)
- [Team & Responsibilities](#team--responsibilities)
- [Architecture](#architecture)
- [Challenge Stage Matrix](#challenge-stage-matrix)
- [Host Prerequisites](#host-prerequisites)
- [Deployment from Scratch](#deployment-from-scratch)
- [Project Structure](#project-structure)
- [Usage](#usage)
- [Admin Console](#admin-console)
- [Reset & Maintenance](#reset--maintenance)
- [License](#license)

---

## Overview

CipherNex-301 is designed as a self-hosted, multi-stage CTF platform with:

- Clean Nginx reverse-proxy routing
- Flask API backend with SQLite telemetry & progress tracking
- JWT / session-based authentication
- Docker-isolated Stage 6 privilege-escalation sandbox
- SHA-256 flag validation
- Real-time admin monitoring and team progress dashboard

The platform emulates a live satellite C2 infrastructure that players must progressively compromise.

---

## Team & Responsibilities

| Role         | Member                  | Component & Deliverables                                                                 |
|--------------|-------------------------|------------------------------------------------------------------------------------------|
| **Member 1** | Platform & Architecture | VPS deployment, Nginx routing, Flask API, SQLite schema, JWT/session auth, Docker Stage 6 isolation, Admin Console (`admin.html`) |
| **Member 2** | Challenge Design A      | **Stages 1–3**: OSINT/Recon (`STG-01`), UDP stream packet reconstruction (`STG-02`), Volatility 3 memory forensics (`STG-03`), challenge files & solver scripts |
| **Member 3** | Challenge Design B      | **Stages 4–6**: Orbital KDF cryptographic decryption (`STG-04`), audio subcarrier steganography (`STG-05`), Linux systemd timer root privilege escalation (`STG-06`) |
| **Member 4** | Integration & Docs      | **Stages 7–8**: Interleaved dual-PCAP correlation (`STG-07`), PBKDF2 multi-parameter capstone (`STG-08`), end-to-end tests, reset automation, documentation |

---

## Architecture

![Architecture Diagram](architecture/image.jpg)

---

## Challenge Stage Matrix

| Stage | Code    | Title                      | Domain              | Difficulty | Points |
|:-----:|:-------:|----------------------------|---------------------|:----------:|:------:|
| **1** | `STG-01` | Skywatch                   | OSINT / Recon       | Easy       | 100    |
| **2** | `STG-02` | Broken Beacon              | Network Forensics   | Easy       | 150    |
| **3** | `STG-03` | Memory of Ground Station   | Memory Forensics    | Moderate   | 200    |
| **4** | `STG-04` | Orbital Cipher             | Cryptography        | Moderate   | 250    |
| **5** | `STG-05` | Hidden in the Noise        | Steganography       | Moderate   | 250    |
| **6** | `STG-06` | Service Ghost              | Linux Security      | Mod-Hard   | 300    |
| **7** | `STG-07` | Command Chain              | Network Crypto      | Hard       | 350    |
| **8** | `STG-08` | Final Downlink             | Capstone / Hybrid   | Hard       | 500    |

**Total possible points:** 2100

---

## Host Prerequisites

- Ubuntu 22.04 LTS (recommended)
- Root / sudo access
- Minimum 2 vCPU / 4 GB RAM (8 GB recommended for comfortable Stage 6 isolation)
- Public IP + domain (optional but recommended for HTTPS)

---

## Deployment from Scratch

### 1. Install System Packages

```bash
sudo apt update && sudo apt install -y \
  git \
  nginx \
  python3 \
  python3-pip \
  python3-venv \
  docker.io \
  curl \
  ufw
```

### 2. Clone the Repository

```bash
git clone https://github.com/TharuPinsara/CipherNex-301.git
cd CipherNex-301
```

### 3. Set Up Python Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 4. Configure Nginx

```bash
sudo cp deploy/nginx/ciphernex.conf /etc/nginx/sites-available/ciphernex
sudo ln -s /etc/nginx/sites-available/ciphernex /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

### 5. Launch Flask API

```bash
# Example using systemd (recommended)
sudo cp deploy/systemd/ciphernex-api.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now ciphernex-api
```

### 6. Build & Start Stage 6 Sandbox

```bash
cd stage6
docker build -t stage6-box .
docker run -d \
  --name stage6-box \
  --memory="512m" \
  --cpus="0.5" \
  -p 2222:22 \
  stage6-box
```

### 7. Firewall Rules (example)

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 2222/tcp
sudo ufw enable
```

---

## Project Structure (suggested)

```
CipherNex-301/
├── app/                    # Flask application
│   ├── api/
│   ├── models/
│   ├── static/
│   └── templates/
├── challenges/
│   ├── stage01/ ... stage08/
├── deploy/
│   ├── nginx/
│   └── systemd/
├── stage6/                 # Docker privilege-escalation box
├── architecture/
│   └── image.jpg           # Architecture diagram
├── docs/
├── scripts/                # Reset & maintenance helpers
├── requirements.txt
└── README.md
```

---

## Usage

1. Navigate to the root portal (`http://your-domain/` or `https://...`).
2. Register / login with a team account.
3. Progress sequentially through the eight stages.
4. Submit flags via the stage interface or the unified API endpoint.
5. Track progress on the live scoreboard / admin dashboard.

---

## Admin Console

Available at `/admin` (protected).

Features:
- Live team progress overview
- Flag submission logs
- Per-stage statistics
- One-click challenge / database reset
- Docker container health monitoring for Stage 6

---

## Reset & Maintenance

Use the provided automation scripts:

```bash
# Full platform reset (flags, progress, containers)
./scripts/reset_all.sh

# Restart only Stage 6 sandbox
./scripts/reset_stage6.sh
```

---

## License

This project is intended for educational and authorized CTF use only.  
Unauthorized deployment against third-party systems is strictly prohibited.

---
