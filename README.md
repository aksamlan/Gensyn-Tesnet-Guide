Gensyn Testnet Node Guide

This guide provides an easy and clean installation process for running a **Gensyn Testnet Node** on GPU servers, VPS environments, or WSL. All steps are simplified, error-free, and fully compatible with the official repository.

---

## 🚀 System Requirements

| Component        | Requirement                          |
| ---------------- | ------------------------------------ |
| CPU Architecture | `arm64` or `amd64`                   |
| RAM              | Recommended: **24 GB+**              |
| GPU (Optional)   | RTX 3090 / 4070 / 4090 / A100 / H100 |
| Python           | **>= 3.10**                          |

> 💡 A GPU is not required, but it significantly increases success rate.

---

## ⚙️ Renting a GPU Server (Quick Pod Example)

1. Visit [Quick Pod](https://quickpod.io/) and sign up with your email.
2. Verify your email address.
3. Add credits to your account (credit card or crypto supported).
4. Select **Ubuntu 22.04 (Jammy)** as the base template.
5. In `Select GPU`, choose for example `RTX 4090`.
6. Click **Create POD**.
7. Once ready, access your machine via **Connect → Web Terminal**.

If you want SSH access, continue with the next section.

---

## 🔐 Connecting via SSH

> Required only if you're running on a GPU provider.

Generate SSH keys:

```bash
ssh-keygen
# Press Enter three times
```

Display your public key and add it to your GPU provider:

```bash
cat ~/.ssh/id_rsa.pub
```

Then connect:

```bash
ssh -p 22 root@YOUR_IP_ADDRESS
```

---

## 🧩 Installation Steps

```bash
# Update system and install sudo
apt update && apt install -y sudo

# Required dependencies
sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip iproute2 build-essential gcc g++ npm

# Install CUDA (for GPU users)
[ -f cuda.sh ] && rm cuda.sh; curl -o cuda.sh https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/cuda.sh && chmod +x cuda.sh && . ./cuda.sh

# Create a screen session
screen -S gensyn

# Clone repository and navigate into it
git clone https://github.com/gensyn-ai/rl-swarm.git && cd rl-swarm

# Create virtual environment and start swarm
python3 -m venv .venv
. .venv/bin/activate
./run_rl_swarm.sh
```

During startup, answer the prompts:

```
Would you like to push models to Hugging Face Hub? [y/N]
```

1. Answer: **N**
2. Answer: **Y**
3. Answer: **ENTER**

---

## 🌍 Accessing the Web UI (Port 3000)

```bash
npm install -g localtunnel
lt --port 3000
```

You will receive a URL such as `https://something.loca.lt`.
Open it in your browser to access the dashboard.

---

## 🔒 Backing Up `swarm.pem`

`swarm.pem` represents your **contributor identity**.
If lost, **all your contribution history will disappear**.

### Easy Backup

```bash
[ -f backup.sh ] && rm backup.sh; curl -sSL -O https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/backup.sh && chmod +x backup.sh && ./backup.sh
```

### Manual Backup

```bash
scp -P 22 root@IP:/root/rl-swarm/swarm.pem ~/Desktop/swarm.pem
```

---

## 🔎 Checking Node Status & Earnings

### View Logs

```bash
screen -r gensyn
```

Detach from screen with **Ctrl+A, D**.

### Check Earnings

Visit:
👉 **[https://gensyn-node.vercel.app/](https://gensyn-node.vercel.app/)**
Enter your Peer-ID to view contributions.

> If you see `Connected EOA Address = 0x0000...`, your node may not be tracked.
> Create a new swarm with a fresh email and clean `swarm.pem`.

---

## 🧰 Troubleshooting

### 🔴 `Daemon failed to start in 15.0 seconds`

```bash
nano $(python3 -c "import hivemind.p2p.p2p_daemon as m; print(m.__file__)")
```

Replace:

```
startup_timeout: float = 15,
```

With:

```
startup_timeout: float = 120,
```

Restart:

```bash
python3 -m venv .venv && . .venv/bin/activate && ./run_rl_swarm.sh
```

---

### 🔴 Out of Memory (OOM Fix)

```bash
sudo fallocate -l 16G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

## 💡 Tips

* Always use `screen` to avoid interruptions.
* Never delete your node without backing up `swarm.pem`.
* Monitor your logs and save your Peer-ID.
* GPU servers give a significantly higher completion rate.

---

## 📁 Recommended Repo Structure

```
├── README.md
├── install.sh
├── backup.sh
└── troubleshooting.md
```

---

## ⚖️ License

This guide is shared under the MIT License — feel free to copy, modify, and distribute.

---

### ✍️ Author

**Huseyin — HusoNode**

Validator | AI & Infrastructure Builder | Web3 Contributor

📧 [contact@husonode.xyz](mailto:contact@husonode.xyz)
🌐 [https://explorer.husonode.xyz](https://explorer.husonode.xyz)
🐙 [https://github.com/aksamlan](https://github.com/aksamlan)
