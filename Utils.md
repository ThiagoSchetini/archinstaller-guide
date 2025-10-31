ARCH UTILS
====================================================
## Archlinux update

Mirrors Update:

	sudo pacman -S reflector # If not already installed
	sudo reflector --verbose --country Brazil,Chile,Argentina --age 12 --protocol https --sort rate --latest 20 --save /etc/pacman.d/mirrorlist

Keyring update:

	sudo pacman-key --refresh-keys

System update incrementaly:

	sudo pacman -Syy
	sudo pacman -Syu

Update only aur packages:

	yay -Sua --debug

Clean pacman + pacaur cache:

	sudo pacman -Sc
 	yay -Sc
	
List orphans + remove (needs sudo su):

	sudo pacman -Rns $(pacman -Qdtq)
 
## Keyring (pacman keys) fucked up recovery:
	
	killall gpg-agent
	sudo rm -rf /etc/pacman.d/gnupg
	pacman-key --init 
	pacman-key --populate
	pacman-key --refresh-keys

If something goes wrong, check if keyring is installed/reinstall:

	pacman -Rns archlinux-keyring
	pacman -S archlinux-keyring


## SSH Key Generation Basic Example

Create one SSH public/private key for your workstation:

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"
	ls -la ~/.ssh
	eval "$(ssh-agent -s)"
	ssh-add ~/.ssh/id_ed25519

Copy the SSH public key (to memory) to use on external services like github, gitlab:

	sudo pacman -S xclip
	xclip -sel clip < ~/.ssh/id_ed25519.pub

 ## Kernel add/remove entries on boot

Set the boot timeout and the default kernel, if you installed two or more:

	ls /boot/loader/entries
	**get the name of file (non mutable part)**

	sudo vim /boot/loader/loader.conf
	timeout 3
	default *_linux-zen*

## sudo add/remove user

How to add user as superuser on sudoers manually

	su
	**password**
	
	visudo /etc/sudoers
	
	**include on it**
	joao ALL=(ALL) ALL
	
	reboot

## SSD & TRIM

Trim auto service check (to SSD's and nvme's):

	**continuous trim is not recommended by UNIX community**
	
	**check if its enabled (you should read *Active: active (waiting)*)**
	systemctl status fstrim.timer
	
	**if not, enable it**
	sudo systemctl enable fstrim.timer
	
	reboot

Manage an existing extra SSD like a data SSD, here is my example:

	sudo groupadd data
	sudo usermod -a -G data $USER
	
	sudo chown -R $USER . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	sudo chgrp -R data . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	
	**get the media path on**
	ls -la /run/media/$USER/

SSD space (check):

	df
	sudo du -h --max-depth=1 /




	
## VPN

VPN. Add the file.ovpn on Settings/Network or use the terminal:

	sudo nmcli connection import type openvpn file /path/to/your.ovpn
	nmcli connection up <connection-name>
	nmcli connection show

	**use the default gateway if your VPN host do not have access to external network**

	sudo ip route add default via <default-route-ip>
	sudo ip route add default via 192.168.15.1

	**get your Default Route on *Settings/Network/Wired-or-WiFi/settings/Details/Default Route**

## systemclt scripts run

How to run script with systemclt (systemd controller). Example:
	
	sudo touch /usr/local/sbin/mytask.sh
	sudo chmod +x /usr/local/sbin/mytask.sh
	
	**put the the shell script on sh file**
	sudo vim /usr/local/sbin/mytask.sh
	
	sudo touch /etc/systemd/system/myactivator.service
	sudo chmod +x /etc/systemd/system/myactivator.service
	
	sudo vim /etc/systemd/system/myactivator.service
	
	**template for the service file**
	[Unit]
	Description=My Task

	[Service]
	ExecStart=/usr/local/sbin/mytask.sh

	[Install]
	WantedBy=multi.user.target
	**template ends**
	
	sudo systemctl enable myactivator.service
	
	reboot

## RGB configuration (openrgb)

Execute:

	sudo lsmod | grep i2c
	sudo sh -c "echo i2c-dev > /etc/modules-load.d/i2c-dev.conf"

Check:

	cat /etc/modules-load.d/i2c-dev.conf
	pacman -Qo /usr/include/linux/i2c-dev.h   

Reboot and then:

	sudo modprobe i2c-dev

openrgb:

	sudo pacman -S openrgb

 Example of terminal usage of openrgb after setting up a device:
 
 	openrgb --device 0 --mode Direct --brightness 70 -c FF0000 -v

## Linux with Windows on same hardware (not dual boot)

Adjust Windows for BOOT with UNIX:

	reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
	
	**natural scroll on windows is good too**
	Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Enum\HID\*\*\Device` Parameters FlipFlopWheel -EA 0 | ForEach-Object { Set-ItemProperty $_.PSPath FlipFlopWheel 1 }
