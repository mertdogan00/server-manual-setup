# 🌐 Complete Linux Server Setup Guide 🛠️

*A powerful all-in-one guide to bootstrap, harden, and optimize your Linux server* 🚀

---

## 📚 Table of Contents

- [Update and Upgrade the System](#-update-and-upgrade-the-system)
- [Install Zsh and Make It Default](#-install-zsh-and-make-it-default)
- [Install Powerline Fonts for Agnoster Theme](#-install-powerline-fonts-for-agnoster-theme)
- [Install Fail2Ban](#-install-fail2ban)
- [Install UFW Firewall and Configure Rules](#-install-ufw-firewall-and-configure-rules)
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

## 🎨 Install Powerline Fonts for Agnoster Theme

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
```

Then edit your `~/.zshrc` file and set:
```
ZSH_THEME="agnoster"
```

Reload:
```bash
source ~/.zshrc
```

Install fonts:
```bash
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
cd ..
rm -rf fonts
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

## 🔥 Install UFW Firewall and Configure Rules

```bash
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
```

---

## 🖥️ Update Hostname

```bash
read -p "Enter the new hostname: " NEW_HOSTNAME
sudo hostnamectl set-hostname "$NEW_HOSTNAME"
sudo nano /etc/hostname
sudo nano /etc/hosts
```

---

## ☁️ Optional: Configure Cloud-Init

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

## ♻️ Final Step: Reboot and Clean Up

```bash
sudo apt autoremove --purge -y
sudo reboot
```

---

✅ Also check:  
👉 [Certbot SSL Setup (Let's Encrypt)](https://github.com/mertdogan00/certbot-self-hosted-ssl)  

---
## 🪪 License 📜

This project is licensed under the MIT License.  
See the [LICENSE](./LICENSE) file for details.
