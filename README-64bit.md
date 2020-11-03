# Create Raspbian Imagels

* http://downloads.raspberrypi.org/raspios_arm64/images/raspios_arm64-2020-08-24/2020-08-20-raspios-buster-arm64.zip


## Install 64bit jdk

```bash
# Update your system
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install screen vim

# Enable ssh server for remote access
sudo systemctl enable ssh
sudo systemctl start ssh

# WIFI...?

# Add your public key to your .ssh/authorized_keys
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
pico .ssh/authorized_keys

# install java
sudo apt-get install openjdk-11-jdk

#-------------------------------------------------------------------------------
# Minecraft paper server
#-------------------------------------------------------------------------------
# Create minecraft user
adduser --system --shell /bin/bash --home /opt/minecraft --group minecraft
# Add minecraft group to the pi user
sudo usermod -a -G minecraft pi

sudo chown -R minecraft:minecraft /opt/minecraft
mkdir /opt/minecraft/1.16.3
ln -s 1.16.3 current
wget https://papermc.io/api/v1/paper/1.16.3/247/download -O 1.16.3/paper-247.jar

# Run paper jar file so you can accept the eula
cd /opt/minecraft/current
java -Xms6G -Xmx6G -jar paper-247.jar

# Edit the eula.txt and change from false to true
echo "eula=true" > eula.txt

# Run the jar file
java -Xms6G -Xmx6G -jar paper-247.jar

# minecraft.service...
# https://minecraft.gamepedia.com/Tutorials/Server_startup_script
# https://pimylifeup.com/raspberry-pi-minecraft-server/
# sudo systemctl daemon-reload
# sudo systemctl start minecraft@current
# sudo systemctl status minecraft@current
# sudo systemctl stop minecraft@current
# sudo systemctl reload minecraft@current

# Rotate Log files...

```
