# Create PicoChessWeb images based on DietPi

This repository uses Ansible to automate the creation of SD card images for PicoChessWeb based on the repository at https://github.com/gkalab/picochess and based on the DietPi distribution for Raspberry Pi.

## Base installation of DietPi

Last DietPi version tested: DietPi v8.14.2

* Download the latest DietPi image for Raspberry Pi (ARMv7 32-bit image or ARMv8 64-bit image) from https://dietpi.com/.
* Flash the image to an SD-card (8GB minimum size).
* Replace the dietpi.txt on the FAT32 partition with the file from the config folder.
* Adjust the config.txt in the same FAT32 partition to your system. Example configurations I am using are in the config folder.
* Enter the SSID and wifi password in the file dietpi-wifi.txt in the same FAT32 partition.

## First boot

Boot the Raspberry Pi with the SD-card, wait for the initial installation to complete (about 15 minutes). Upon completion you should see a running Chromium browser with a page load error.

Make sure you can connect to the Raspberry Pi via ssh as root (see root password in ansible/files/boot/dietpi.txt).

## Update engines, audio files (optional)

From an existing PicoChess installation:
* Copy the files from the talker directory into the ansible/files/talker/ folder of this repository.
* Copy engine binaries from an existing PicoChess installation to the files/engines/ directory. The files/engines/ directory will get copied to /opt/picochess/engines/ on the device. Make sure to include the correct engine binaries for your platform.

## Adjust picochess.ini file (optional)

Some PicoChess options can only be set via the picochess.ini file. Adjust the settings in ansible/files/opt/picochess/picochess.ini for your system.

## Add Komodo 13.02 engine binary (optional, only if ARMv8 64-bit image is used)

Download Komodo 13 from https://komodochess.com/pub/komodo-13.zip and place the included komodo-13.02-linux in the directory ansible/files/extra_engines/komodo13/ .

## Add Pedone 3.0 engine binary (optional, only if ARMv8 64-bit image is used)

Download Pedone 3.0 from https://drive.google.com/file/d/17ImH_wz4b5FAvDJH_UTHfQkjk5Ijl0vv/view?usp=sharing and place the included Pedone_armv8 in the directory ansible/files/extra_engines/pedone/ .

## Installation

```shell
pip3 install ansible
apt-get install sshpass rsync
```

* Install requirements: `cd ansible; ansible-galaxy collection install -r requirements.yml`
* Make sure you can connect to your Raspberry Pi via ssh: `ssh root@<IP-address>`
* Replace <raspberrypi-address> with the IP address of your Raspberry Pi in the file inventory.ini

## Start Ansible

Run the playbook: `ansible-playbook --ask-vault-pass main.yml`

You can run the playbook again if you update engine or audio files. The first run on a freshly installed system takes a little over 20 minutes using wifi.

## Cleanup

Check the /boot/config.txt file for changes. I need to remove the line
dtoverlay=vc4-kms-v3d
to make audio work over HDMI on my system.
