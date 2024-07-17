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

```
nano fix.sh
```
```
#!/bin/bash

# Version 1.1

# Define the service file path
SERVICE_FILE="/etc/systemd/system/default-interface-config.service"

# Check if the script is run with root privileges
if [ "$EUID" -ne 0 ]; then
  echo "Please run as root."
  exit 1
fi

# Install ethtool if it's not already installed
if ! command -v ethtool &> /dev/null; then
  echo "ethtool not found, installing..."
  apt-get update
  apt-get install -y ethtool
fi

# Get the default network interface
DEFAULT_INTERFACE=$(ip route show default | awk '/default/ {print $5}')

# Create the systemd service file
cat <<EOL > $SERVICE_FILE
[Unit]
Description=Configure default network interface
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -s $DEFAULT_INTERFACE speed 1000 duplex full autoneg off
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOL

# Reload systemd to recognize the new service
systemctl daemon-reload

# Enable the service to start on boot
systemctl enable default-interface-config.service

# Start the service immediately
systemctl start default-interface-config.service

echo "Default interface configuration service loaded and started on $DEFAULT_INTERFACE."
```
```
chmod +x fix.sh
```
```
./fix.sh
```

