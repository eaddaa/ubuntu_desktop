# ubuntu_desktop
```
mkdir ubuntu_desktop
cd ubuntu_desktop
```
```
nano ubuntu_desktop.sh
```

```
#!/bin/bash

# Define variables for remote desktop user and password
# Avoid using root
USER="USERNAME"
PASSWORD="PASSWORD"

# Update package lists
sudo apt update && sudo apt upgrade -y

# Install GNOME Desktop
sudo apt install -y ubuntu-desktop

# Install remote desktop server (xrdp)
sudo apt install -y xrdp

# Add USER with specified password
sudo useradd -m -s /bin/bash $USER
echo "$USER:$PASSWORD" | sudo chpasswd

# Add USER to sudo group for administrative privileges
sudo usermod -aG sudo $USER

# Configure xrdp to use GNOME session for USER
echo "gnome-session" > /home/$USER/.xsession
sudo chown $USER:$USER /home/$USER/.xsession

# Restart xrdp service
sudo systemctl restart xrdp

# Enable xrdp to start on boot
sudo systemctl enable xrdp

echo "Ubuntu desktop setup completed. Please use your USERNAME and PASSWORD to connect to the server."
```
```
chmod +x ubuntu_desktop.sh
```
```
./ubuntu_desktop.sh
```
