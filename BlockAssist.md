# BlockAssist – Quick Installation Guide

BlockAssist is an AI agent that learns from your actions inside Minecraft. It starts simple, but improves as you play and complete building tasks. This README provides a clean, simplified, and non‑duplicative installation guide.

---

## ✅ Requirements

**Hardware**

* 8 GB RAM (16 GB recommended)
* Multi‑core CPU
* 100 GB disk space

**Recommended Environment**

* Local Linux system

(WSL, macOS, VirtualBox, and cloud GPU environments work, but may provide slower gameplay.)

---

## 🚀 Installation

### 1) Install Dependencies

```bash
sudo apt update
sudo apt install -y \
  make build-essential gcc \
  libssl-dev zlib1g-dev libbz2-dev libreadline-dev \
  libsqlite3-dev libncursesw5-dev xz-utils tk-dev \
  libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev \
  curl git unzip libxi6 libxrender1 libxtst6 libxrandr2 \
  libglu1-mesa libopenal1
```

### 2) Clone Repository

```bash
git clone https://github.com/gensyn-ai/blockassist.git
cd blockassist
```

### 3) Install Node.js 20

```bash
node --version
sudo apt remove -y nodejs npm
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

### 4) Install Yarn

```bash
curl -o- -L https://yarnpkg.com/install.sh | bash
export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"
```

### 5) Install Java

```bash
./setup.sh
exec $SHELL
```

### 6) Install pyenv

```bash
curl https://pyenv.run | bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```

### 7) Install Python 3.10

```bash
pyenv install 3.10
pyenv global 3.10
```

### 8) Install Python Dependencies

```bash
pip install --upgrade pip
pip install -e . --no-cache-dir
pip install "mbag-gensyn[malmo]" --no-cache-dir
pip install psutil readchar
```

---

## ▶️ Run BlockAssist

Start Minecraft, then run:

```bash
cd blockassist
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv.init --path)"
eval "$(pyenv init -)"
python run.py
```

### First‑Run Steps

* Enter your **Hugging Face token** (must have *Write* permission).
* Log in to Gensyn at **[http://localhost:3000](http://localhost:3000)**.
* After two Minecraft windows appear, press **ENTER** in terminal.
* Build the structure shown in the game (more progress → better AI training).
* Return to terminal and press **ENTER** again to finish.

Your model will be trained and submitted automatically.

---

## 📊 Leaderboard

You can check your participation on the official BlockAssist [Leaderboard](https://dashboard.gensyn.ai/).

---

## ⚙️ Configuration

Edit `src/blockassist/config.yaml` or use CLI flags.

Key options:

* `episode_count`: number of gameplay episodes recorded
* `num_training_iters`: total training iterations across episodes

---

## 🛠 Troubleshooting

### Increase Malmo Startup Timeout

```bash
sed -i 's#python -m malmo.minecraft launch#python -m malmo.minecraft launch --timeout 300#' scripts/run_malmo.sh
```

### View Logs

```bash
# Game logic
tail -f logs/malmo.log

# Minecraft startup
tail -n 200 logs/run.log

# Login server
tail -n 200 logs/yarn.log
```

# Gensyn Discord : [Here](https://discord.gg/8fqs6AMa)
# Gensyn Twitter : [Here](https://x.com/gensynai)
