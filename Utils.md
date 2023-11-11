ARCH UTILS
====================================================
	
# Archlinux update

	sudo pacman-key --refresh-keys
 	sudo pacman -Sy archlinux-keyring

  	**Syu = Update package list and upgrade all packages afterwards**
	sudo pacman -Syu --debug
   	pacaur -Syu --debug

 	**clean pacman + pacaur cache**
	sudo pacman -Sc
 	pacaur -Sc
	
	**list orphans + remove (needs sudo su)**
	sudo pacman -Qtdq | xargs -ro pacman -Rs
	
	**only remove the orphans**
	sudo pacman -R aaa bbb ccc ...
 
# Keyring (pacman keys) fucked up recovery:
	
	killall gpg-agent
	sudo rm -rf /etc/pacman.d/gnupg
	pacman-key --init 
	pacman-key --populate
	pacman -Sy archlinux-keyring

	And if that fails, do:
	pacman-key --refresh-keys
	pacman -Sy archlinux-keyring

	Hint: If refresh keys takes more than a minute just hit ctrl + c and continue, it likely has still worked.

# Mirrorlist Manual Server Add/Remove

Generate and replace (ry to choose more than one country, like yours and EUA):

	**Generate and remove all comments "#" on some editor
 	https://archlinux.org/mirrorlist/

 	sudo vim etc/pacman.d.mirrorlist
  	**shift+v to visual mode, them select all with arrows them "x" to clean**	

# SSH

Create one SSH public/private key for your workstation:

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"
	ls -la ~/.ssh
	eval "$(ssh-agent -s)"
	ssh-add ~/.ssh/id_ed25519

Copy the SSH public key (to memory) to use on external services like github, gitlab:

	sudo pacman -S xclip
	xclip -sel clip < ~/.ssh/id_ed25519.pub

# SSD & TRIM

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

# Linux with Windows on same hardware

Adjust Windows for BOOT with UNIX:

	reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
	
	**natural scroll on windows is good too**
	Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Enum\HID\*\*\Device` Parameters FlipFlopWheel -EA 0 | ForEach-Object { Set-ItemProperty $_.PSPath FlipFlopWheel 1 }

# Kernel

Set the boot timeout and the default kernel, if you installed two or more:

	ls /boot/loader/entries
	**get the name of file (non mutable part)**

	sudo vim /boot/loader/loader.conf
	timeout 3
	default *_linux-zen*

# sudo

How to add user as superuser on sudoers manually

	su
	**password**
	
	visudo /etc/sudoers
	
	**include on it**
	joao ALL=(ALL) ALL
	
	reboot
	
# VPN

VPN. Add the file.ovpn on Settings/Network or use the terminal:

	sudo nmcli connection import type openvpn file /path/to/your.ovpn
	nmcli connection up <connection-name>
	nmcli connection show

	**use the default gateway if your VPN host do not have access to external network**

	sudo ip route add default via <default-route-ip>
	sudo ip route add default via 192.168.15.1

	**get your Default Route on *Settings/Network/Wired-or-WiFi/settings/Details/Default Route**

# Systemd scripts

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
