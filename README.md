# 🌐 Complete Linux Server Setup Guide 🛠️

*A powerful all-in-one guide to bootstrap, harden, and optimize your Linux server* 🚀

---

## 📚 Table of Contents

- [Update and Upgrade the System](#-update-and-upgrade-the-system)
- [Install Zsh and Make It Default](#-install-zsh-and-make-it-default)
- [Install and Configure Oh My Zsh](#-install-and-configure-oh-my-zsh)
- [Install Zsh Plugins](#-install-zsh-plugins)
- [Install Fail2Ban](#-install-fail2ban)
- [Optional: Configure Fail2Ban for NGINX](#-optional-configure-fail2ban-for-nginx)
- [Firewall Configuration (Choose UFW or firewalld)](#-firewall-configuration-choose-ufw-or-firewalld)
- [Update Hostname](#-update-hostname)
- [Optional: Configure Cloud-Init](#-optional-configure-cloud-init)
- [Optional: Install Docker](#-optional-install-docker)
- [Final Step: Reboot and Clean Up](#-final-step-reboot-and-clean-up)

---

## 🧱 Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🐚 Install Zsh and Make It Default

```bash
sudo apt install -y zsh curl git
chsh -s $(which zsh)
```

---

## 🎨 Install and Configure Oh My Zsh

Install Oh My Zsh:
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
```

Set theme and plugins in `~/.zshrc`:
```bash
ZSH_THEME="bira"
plugins=(git zsh-autosuggestions zsh-syntax-highlighting zsh-autocomplete)
```

Reload:
```bash
source ~/.zshrc
```

---

## ✨ Install Zsh Plugins

Install required plugins:
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/marlonrichert/zsh-autocomplete.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autocomplete
```

Reload Zsh:
```bash
source ~/.zshrc
```

---

## 🔐 Install Fail2Ban

Make sure `rsyslog` is installed:

```bash
sudo apt install -y rsyslog
sudo systemctl enable --now rsyslog
```

Then install Fail2Ban:

```bash
sudo apt install -y fail2ban
sudo systemctl enable --now fail2ban
```

Check status:
```bash
sudo fail2ban-client status
```

---

## 🛡 Optional: Configure Fail2Ban for NGINX

You can enhance your NGINX protection by creating custom jail configurations inside the `jail.d` directory.

🗂️ Example file path: `/etc/fail2ban/jail.d/nginx.conf`

Here’s a complete example configuration:

```ini

[nginx-botsearch]
enabled  = true
filter   = nginx-botsearch
port     = http,https
logpath  = /var/log/nginx/access.log
           /var/log/nginx/cp-main_access_log
           /var/log/nginx/cp-main_error_log
maxretry = 10
bantime  = 86400

[nginx-http-auth]
enabled  = true
filter   = nginx-http-auth
port     = http,https
logpath  = /var/log/nginx/error.log
           /var/log/nginx/cp-main_access_log
           /var/log/nginx/cp-main_error_log
maxretry = 3
bantime  = 3600

[nginx-bad-request]
enabled  = true
filter   = nginx-bad-request
port     = http,https
logpath  = /var/log/nginx/access.log
           /var/log/nginx/cp-main_access_log
           /var/log/nginx/cp-main_error_log
maxretry = 5
bantime  = 1800

[nginx-limit-req]
enabled  = true
filter   = nginx-limit-req
port     = http,https
logpath  = /var/log/nginx/error.log
           /var/log/nginx/cp-main_access_log
           /var/log/nginx/cp-main_error_log
maxretry = 10
bantime  = 3600

```

✅ After saving the configuration, apply the changes with:
```bash
sudo systemctl restart fail2ban

```


## 🔥 Firewall Configuration (Choose UFW or firewalld)

You can choose **UFW** (simple) or **firewalld** (advanced).  
Both are supported on Debian. SSH port (22) is **open by default** on Debian systems.

---

### 🔸 Option 1: UFW (Recommended for Simplicity)

#### Install UFW:
```bash
sudo apt install -y ufw
```

#### Set Default Policies:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

#### Open Essential Ports:
```bash
sudo ufw allow ssh comment 'Allow SSH for remote access'
sudo ufw allow 2083/tcp comment 'Allow port 2083 for cPanel/Webmin SSL access'
sudo ufw allow 80/tcp comment 'Allow HTTP traffic'
sudo ufw allow 443/tcp comment 'Allow HTTPS traffic'

```

#### Enable UFW:
```bash
sudo ufw enable
```

#### Check UFW Status:
```bash
sudo ufw status verbose
```

---

### 🔸 Option 2: firewalld (Recommended for Advanced Users)

#### Install firewalld:
```bash
sudo apt install -y firewalld
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```
⚡ On Debian, firewalld may start automatically upon installation. Stop and disable it immediately to prevent accidental lockout.

#### Open Essential Ports:
```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-port=2083/tcp
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
```

#### Apply Configuration and Start firewalld:
```bash
sudo firewall-cmd --reload
sudo systemctl enable firewalld
sudo systemctl start firewalld
```

#### Check firewalld Status:
```bash
sudo firewall-cmd --list-all
```

---

### 🔒 Important Note:
If you lock yourself out, most VPS providers offer a **Reset Firewall** option in their dashboard to regain access.

---

## 🖥 Update Hostname

```bash
read -p "Enter the new hostname: " NEW_HOSTNAME
sudo hostnamectl set-hostname "$NEW_HOSTNAME"
sudo nano /etc/hostname
sudo nano /etc/hosts
```

---

## ☁ Optional: Configure Cloud-Init

```bash
sudo nano /etc/cloud/cloud.cfg
```

Set:
```
preserve_hostname: true
```

Then run:
```bash
sudo cloud-init clean
sudo cloud-init init
```

---

## 🐳 Optional: Install Docker

Remove old Docker versions:
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
  sudo apt-get remove -y $pkg || true
done
```

Install Docker:
```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Enable and start:
```bash
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

Check:
```bash
docker --version
```

---

## ♻ Final Step: Reboot and Clean Up

```bash
sudo apt autoremove --purge -y
sudo reboot
```

---

✅ Also check:  
👉 [Certbot SSL Setup (Let's Encrypt)](https://github.com/mertdogan00/certbot-self-hosted-ssl)  

---

## 🤝 Contributing

Contributions are welcome! Feel free to submit pull requests to improve this guide. 🎉🔥

---

## 🪪 License 📜

This project is licensed under the MIT License.  
See the [LICENSE](./LICENSE) file for details.
