# 🧠 Gensyn Testnet Node Guide

Bu rehber, **Gensyn Testnet Node** kurulumunu GPU, VPS veya WSL ortamlarında kolayca gerçekleştirebilmeniz için hazırlanmıştır. Adımlar sadeleştirilmiş, hatalardan arındırılmış ve repo uyumlu hale getirilmiştir.

---

## 🚀 Sistem Gereksinimleri

| Bileşen         | Gereksinim                           |
| --------------- | ------------------------------------ |
| CPU Mimarisi    | `arm64` veya `amd64`                 |
| RAM             | Önerilen: **24 GB+**                 |
| GPU (Opsiyonel) | RTX 3090 / 4070 / 4090 / A100 / H100 |
| Python          | **>= 3.10**                          |

> 💡 GPU zorunlu değildir, ancak yüksek başarı oranı için önerilir.

---

## ⚙️ GPU Kiralama (Quick Pod Örneği)

1. [Quick Pod](https://quickpod.io/) sitesine gidin ve e-posta ile kayıt olun.
2. E-postanızı doğrulayın.
3. Hesabınıza bakiye ekleyin (kredi kartı veya kripto ile).
4. Template olarak **Ubuntu 22.04 (Jammy)** seçin.
5. `Select GPU` kısmında örneğin `RTX 4090` seçin.
6. `Create POD` butonuna tıklayın.
7. Sunucu hazır olduğunda **Connect → Web Terminal** üzerinden bağlanabilirsiniz.

SSH ile bağlanmak istiyorsanız, aşağıdaki adımları takip edin.

---

## 🔐 SSH ile Bağlanma

> Bu adım sadece GPU üzerinde çalıştırıyorsanız gereklidir.

```bash
ssh-keygen
# Enter tuşuna 3 kez basın
```

Ardından public key’i görüntüleyin ve GPU sağlayıcınıza ekleyin:

```bash
cat ~/.ssh/id_rsa.pub
```

Daha sonra GPU instance’ınıza şu komutla bağlanın:

```bash
ssh -p 22 root@IP_ADRESINIZ
```

---

## 🧩 Kurulum Adımları

```bash
# Güncelleme ve sudo kurulumu
apt update && apt install -y sudo

# Gerekli bağımlılıklar
sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip iproute2 build-essential gcc g++ npm

# CUDA kurulumu (GPU için)
[ -f cuda.sh ] && rm cuda.sh; curl -o cuda.sh https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/cuda.sh && chmod +x cuda.sh && . ./cuda.sh

# Screen oturumu oluştur
screen -S gensyn

# Repo klonla ve dizine gir
git clone https://github.com/gensyn-ai/rl-swarm.git && cd rl-swarm

# Sanal ortam ve başlatma
python3 -m venv .venv
. .venv/bin/activate
./run_rl_swarm.sh
```

Kurulum sonrası sorulan sorulara:

```
Would you like to push models to Hugging Face Hub? [y/N]
```

1. Cevabı: **N**
2. Cevabı: **Y**
3. Cevabı: **ENTER**
---

## 🌍 Web Arayüzüne Erişim (Port 3000)

```bash
npm install -g localtunnel
lt --port 3000
```

Terminal size `https://something.loca.lt` gibi bir URL dönecektir. Bu bağlantıyı tarayıcıda açarak arayüze erişebilirsiniz.

---

## 🔒 swarm.pem Yedekleme

`swarm.pem` dosyası **katkı kimliğinizi** temsil eder — kaybederseniz katkınız silinir.

### Kolay Yedekleme

```bash
[ -f backup.sh ] && rm backup.sh; curl -sSL -O https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/backup.sh && chmod +x backup.sh && ./backup.sh
```

### Manuel Yedekleme

```bash
scp -P 22 root@IP:/root/rl-swarm/swarm.pem ~/Desktop/swarm.pem
```

---

## 🔎 Node Durumu ve Kazanç Kontrolü

### Logları Görüntüleme

```bash
screen -r gensyn
```

Loglar çalışıyorsa, **Ctrl+A, D** ile ayrılabilirsiniz.

### Kazançları Kontrol Etme

Peer-ID’nizi kullanarak [Gensyn Node Tracker](https://gensyn-node.vercel.app/) adresinden kontrol edebilirsiniz.

> Eğer `Connected EOA Address = 0x0000...` görüyorsanız node katkınız kaydedilmiyor olabilir. Yeni e-posta ve temiz `swarm.pem` ile yeniden başlatın.

---

## 🧰 Sorun Giderme

### 🔴 `Daemon failed to start in 15.0 seconds`

```bash
nano $(python3 -c "import hivemind.p2p.p2p_daemon as m; print(m.__file__)")
```

Açılan dosyada:

```
startup_timeout: float = 15,
```

Satırını şu şekilde değiştirin:

```
startup_timeout: float = 120,
```

Kaydedin (**Ctrl+X → Y → Enter**) ve swarm’u yeniden başlatın:

```bash
python3 -m venv .venv && . .venv/bin/activate && ./run_rl_swarm.sh
```

### 🔴 Bellek Sorunları (OOM)

```bash
sudo fallocate -l 16G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

## 💡 İpuçları

* Her zaman `screen` kullanın, terminal kapanınca süreç kesilmez.
* `swarm.pem` dosyasını yedeklemeden node’u silmeyin.
* Logları düzenli takip edin, Peer-ID’nizi not alın.
* GPU kiralayabiliyorsanız çok daha yüksek başarı oranı elde edersiniz.

---

## 📁 Repo Dosya Önerileri

```
├── README.md
├── install.sh
├── backup.sh
└── troubleshooting.md
```

---

## ⚖️ Lisans

MIT Lisansı altında paylaşılmıştır. Dilediğiniz gibi kopyalayabilir, geliştirebilir ve paylaşabilirsiniz.

---

### ✍️ Hazırlayan

**Huseyin — HusoNode**

> Validator | AI & Infrastructure Builder | Web3 Contributor

📧 [contact@husonode.xyz](mailto:contact@husonode.xyz)
🌐 [explorer.husonode.xyz](https://explorer.husonode.xyz)
🐙 [github.com/aksamlan](https://github.com/aksamlan)
