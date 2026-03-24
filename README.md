# 🐧 Ubuntu 26.04 – Frappe Framework Installation Guide

## 📋 Pre-requisites

| Dependency | Frappe v14/v15 | Frappe v16 / develop |
| ---------- | -------------- | -------------------- |
| MariaDB    | 10.6.6+        | 11.8                 |
| Python     | 3.10+          | 3.14                 |
| Node.js    | 18+            | 24                   |
| Redis      | 6              | 6+                   |
| Yarn       | 1.12+          | 1.22+                |
| pip        | 20+            | 25.3+                |

---

## ⚙️ Step 1: Install Node.js 24

```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install nsolid -y
```

---

## 📦 Step 2: Install System Dependencies

```bash
sudo apt install -y \
  git \
  redis-server \
  libmariadb-dev \
  mariadb-server \
  mariadb-client \
  pkg-config
```

---

## 🔐 Step 3: Secure MariaDB Installation

```bash
sudo mariadb-secure-installation
```

---

## 🖨️ Step 4: Install wkhtmltopdf Dependencies

```bash
sudo apt install -y \
  xvfb \
  libfontconfig \
  libjpeg-turbo8 \
  xfonts-75dpi
```

---

## 📄 Step 5: Install wkhtmltopdf

```bash
wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-3/wkhtmltox_0.12.6.1-3.jammy_amd64.deb

sudo dpkg -i wkhtmltox_0.12.6.1-3.jammy_amd64.deb

sudo cp /usr/local/bin/wkhtmlto* /usr/bin/
sudo chmod a+x /usr/bin/wk*

sudo rm wk*

sudo apt --fix-broken install -y
```

---

## 🧶 Step 6: Install Yarn

```bash
sudo npm install -g yarn
```

---

## 🔄 Step 7: Install NVM (Node Version Manager)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Then reload shell:

```bash
source ~/.bashrc
```

Install Node.js 24 using NVM:

```bash
nvm install 24
```

---

## 🧶 Step 8: Install Yarn (via NVM Node)

```bash
npm install -g yarn
```

---

## 🐍 Step 9: Install UV (Python Manager)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## 🐍 Step 10: Install Python 3.14

```bash
uv python install 3.14 --default
```

---

## 🛠️ Step 11: Install Build Essentials

```bash
sudo apt update

sudo apt install -y \
  build-essential \
  libmariadb-dev \
  libmariadb-dev-compat
```

---

## 🧱 Step 12: Install Bench (Frappe CLI)

```bash
uv tool install frappe-bench
```

---

## ✅ Next Steps

After installation, you can initialize a bench:

```bash
bench init frappe-bench --frappe-branch version-16
cd frappe-bench
bench new-site mysite.localhost
bench get-app erpnext
bench --site mysite.localhost install-app erpnext
bench start
```
