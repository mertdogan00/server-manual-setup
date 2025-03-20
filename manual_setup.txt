# 🌐 Complete Server Setup Guide 🎯
# Follow these steps to configure your Linux server with Zsh, Oh-My-Zsh, Fail2Ban, UFW, and Docker. 

---

## 1️⃣ Update and Upgrade the System
# 🛠️ Keep your system up-to-date by running this command:
sudo apt update && sudo apt upgrade -y

---

## 2️⃣ Install Zsh and Make It Default
# 🐚 Install Zsh:
sudo apt install -y zsh curl git

# 🔧 Make Zsh the default shell:
chsh -s $(which zsh)

---

## 3️⃣ Install Oh-My-Zsh
# ⚡ Oh-My-Zsh is a powerful framework for Zsh. Install it with:
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

---

## 4️⃣ Set the Agnoster Theme
# 🎨 Open the `.zshrc` file to configure the theme:
nano ~/.zshrc

# 🔸 In the file, find the line starting with `ZSH_THEME=` and change it to:
# ZSH_THEME="agnoster"

# ✅ Save and exit (CTRL+O, ENTER, CTRL+X).

# 🔄 Reload Zsh to apply the changes:
source ~/.zshrc

---

## 5️⃣ Install Powerline Fonts (for Agnoster Theme)
# 🖋️ Download and install Powerline fonts:
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
cd ..
rm -rf fonts

---

## 6️⃣ Install Fail2Ban
🔹 Prerequisite: Install Rsyslog (if not installed)
Fail2Ban requires log files (e.g., `/var/log/auth.log`) to function properly.  
Some minimal Debian installations do not include `rsyslog` by default.  
To ensure logs are available, install and enable rsyslog first:  

# Install rsyslog if it’s not installed
sudo apt update && sudo apt install -y rsyslog  

# Enable and start rsyslog service
sudo systemctl enable rsyslog  
sudo systemctl start rsyslog  

# Verify rsyslog is running
sudo systemctl status rsyslog 

# 🔐 Fail2Ban protects your server from brute-force attacks. Install it with:
sudo apt install -y fail2ban

# 🟢 Enable and start Fail2Ban:
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# ⚙️ (Optional) Edit Fail2Ban settings:
sudo nano /etc/fail2ban/jail.local

# 📊 Check Fail2Ban status:
sudo fail2ban-client status

---

## 7️⃣ Install UFW (Firewall) and Configure Rules
# 🛡️ Install UFW:
sudo apt install -y ufw

# 🔧 Set default UFW rules:
sudo ufw default deny incoming
sudo ufw default allow outgoing

# ✅ Allow SSH traffic:
sudo ufw allow ssh

# 🔒 Enable UFW:
sudo ufw enable

# 📊 Show UFW status (Verbose):
sudo ufw status verbose

---

## 8️⃣ Update Hostname
# 🖥️ Update the server's hostname:
read -p "Enter the new hostname: " NEW_HOSTNAME
sudo hostnamectl set-hostname "$NEW_HOSTNAME"

# ✏️ Edit the `/etc/hostname` file and replace its content with the new hostname:
sudo nano /etc/hostname

# ✏️ Edit the `/etc/hosts` file to update the hostname mapping:
sudo nano /etc/hosts

---

## 9️⃣ (Optional) Configure Cloud-Init
# ☁️ If your server uses Cloud-Init, make hostname changes persistent:
sudo nano /etc/cloud/cloud.cfg

# 🔸 In the file, find the line `preserve_hostname: false` and change it to:
# preserve_hostname: true

# 🚿 Clean and reinitialize Cloud-Init:
sudo cloud-init clean
sudo cloud-init init

---

## 🔟 (Optional) Install Docker
# 🐳 Docker is a platform for containerized applications.

# 🔧 Remove conflicting packages:
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
  sudo apt-get remove -y $pkg || true
done

# 🟢 Install Docker prerequisites:
sudo apt-get update
sudo apt-get install -y ca-certificates curl

# 🔑 Add Docker's official GPG key:
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 📦 Add Docker's repository:
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 🛠️ Install Docker:
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 🟢 Enable and start Docker:
sudo systemctl enable docker
sudo systemctl start docker

# 👤 Add your user to the Docker group (logout and login for it to take effect):
sudo usermod -aG docker $USER

# ✅ Verify Docker installation:
docker --version

---

## 🔁 Final Step: Reboot and Clean Up
# ✨ Clean up unnecessary packages:
sudo apt autoremove --purge -y

# 🔄 Reboot the server to apply all changes:
sudo reboot
