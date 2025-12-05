# Gensyn CodeAssist — Ubuntu 22.04 Desktop

## Overview

CodeAssist is a fully local AI coding assistant that runs on your machine and keeps all data private. This guide covers a quick automated install and a manual alternative, plus basic usage, troubleshooting, and update commands.

## Key features

* Runs fully locally (no cloud data sharing)
* Context-aware code completion and suggestions
* Learns from your edits over time (local training)
* Browser-based interface (default at `http://localhost:3000`)

## System requirements

**Minimum**

* CPU: 4 cores
* RAM: 8 GB
* Disk: 20 GB free (SSD recommended)
* OS: Ubuntu 22.04 LTS Desktop

**Recommended**

* CPU: 8+ cores
* RAM: 16 GB
* Disk: 50 GB+ (SSD)
* Optional GPU with CUDA for faster training/inference

---

## Quick automated install

Open a terminal (Ctrl+Alt+T) and run:

```bash
# download installer
wget https://raw.githubusercontent.com/Edsny1/Gensyn-CodeAssist---Ubuntu-Desktop-Kurulum-Rehberi/refs/heads/Edsny/install.sh -O install-codeassist.sh

# make executable and run
chmod +x install-codeassist.sh
./install-codeassist.sh
```

The script will update your system, install dependencies, configure Docker, install the `uv` runtime, and pull CodeAssist. You will be prompted for your password for `sudo` actions.

---

## Manual install (step-by-step)

1. Update the system

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl wget build-essential python3 python3-pip python3-venv
```

2. Install and enable Docker

```bash
sudo apt install -y docker.io docker-compose
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker
docker --version
```

3. Install `uv` runtime

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
uv --version
```

4. Clone CodeAssist

```bash
cd ~
git clone https://github.com/gensyn-ai/codeassist.git
cd codeassist
```

5. Create a Hugging Face write-scoped access token

* Sign in at [https://huggingface.co](https://huggingface.co) → Settings → Access Tokens → New token (grant `write` if asked)
* Save the token securely

6. Start the app

```bash
uv run run.py
```

Open your browser at `http://localhost:3000`.

---

## First use

* Sign in with email or Google
* Enter your Hugging Face token when prompted
* Choose difficulty (Easy / Medium / Hard)
* Start coding — suggestions appear automatically

  * `Tab` to accept a suggestion
  * `Esc` to dismiss
  * `Shift+Space` to temporarily pause suggestions

Training happens automatically from local interactions. To trigger a training cycle, stop the app (Ctrl+C) — training will start if configured — then restart.

---

## Troubleshooting

* **Port 3000 in use:**

  ```bash
  sudo lsof -i :3000
  sudo kill -9 <PID>
  ```
* **Docker issues:** check `sudo systemctl status docker` and `docker ps -a` and inspect logs: `docker logs <container>`
* **uv not found:** ensure `~/.local/bin` is in PATH and re-run the installer
* **Hugging Face token problems:** verify token in `persistent-data/auth/userKeyMap.json` and test `ping -c4 huggingface.co`

---

## Updating CodeAssist

```bash
cd ~/codeassist
git stash --include-untracked
git pull origin main
git stash pop || true
uv run run.py
```

Also keep system packages and Docker updated with `sudo apt update && sudo apt upgrade -y`.

---

## Helpful tweaks

* Enable TRIM for SSDs: `sudo systemctl enable --now fstrim.timer`
* Reduce swappiness: `echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`
* Limit Docker logs by creating `/etc/docker/daemon.json` with a small `max-size` and `max-file` then `sudo systemctl restart docker`.

---

## Directory structure (typical)

```
~/codeassist/
├─ persistent-data/
│  ├─ auth/
│  ├─ trainer/models/
│  └─ sessions/
├─ src/
├─ requirements.txt
└─ run.py
```

## Security reminders

* Keep your Hugging Face token private
* Do not commit `persistent-data/auth/` to any public repo
* Regularly patch your system

---
