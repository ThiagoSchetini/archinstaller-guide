### ARCH INSTALLER GUIDED GUIDE (OMG!) ###
	
	* REMOVE BEFORE START! all partitions of the destination disk before start! archinstall CAN'T DO IT
		parted help
		parted rm ...
		
	* CREATE BEFORE START! if you just security erased, create the partition table GPT! archinstall CAN'T DO IT
		parted mktable GPT
	
	* START the installer
		archinstall
		
	* mirror near from you, ex: Brazil
	
	* select the "manual mode on disk"
		create two partitions:
		fat32/5243kB/513MB
		Ext4/518MB/100%
		mount the first to /boot
		mount the second to /
		mark the both to be formated
		review everything at the end (including boot=true on the first one)
		
	* choose systemd as boot (modern)
	
	* no swap (if 32GB+ RAM)
	
	* hostname: the machine name, like (Microcraft or WorkstationX)
	
	* password on sudo please!
	
	* create an user, like (joao), mark it a superuser (probably)
	
	* on selecting profile desktop:
		use nouveou for OLD NVIDIA graphics! (open source)
		the prorietary will not work on old cards!
		
	* choose pipewire as audio server (modern)
	
	* choose kernel linux-zen (+performance, +power, -throughput) 
		check on: https://liquorix.net/ 
		
	* Aditional Packages: firefox
	
	* Network: Use NetworkManager
		
	* Timezone: America/Sao_Paulo
	
	* Optional Repositories: 'multilib'
	
	Do it!
	
# First if you forgot multilib, enable it manually:

	Uncoment the multilib session from your pacman (both lines):
	
		sudo vim /etc/pacman.conf
		
		[multilib]
		Include = /etc/pacman.d/mirrorlist

# Pacman keys update

	sudo pacman -Syy
	sudo pacman-key --refresh-keys

# VIM as default editor
	
	su
	*password
	
	vim /etc/environment 
	include on it:
	EDITOR=/usr/bin/vim	
	
	reboot

# Default kernel set

	read entries on
	ls /boot/loader/entries
	get the name of file (non mutable part)

	sudo vim /boot/loader/loader.conf
	timeout 3
	default *_linux-zen*
	
# source/devel zen (required to install nvidia drivers)

	sudo pacman -S linux-zen-headers
	
# GIT

	sudo pacman -S git
	mkdir ~/Repositories
	mkdir ~/Repositories/github
	mkdir ~/Repositories/gitbucket
	
# Pacaur

	git clone  https://aur.archlinux.org/auracle-git.git
	cd auracle-git/
	makepkg -si

	sudo pacman -S expac jq

	git clone https://aur.archlinux.org/pacaur.git
	cd pacaur
	makepkg -si

	cd ~/
	rm -r pacaur
	rm -r auracle-git
	
# Gnome System Tray (to use apps on background like sensors and DSP)

	git clone https://aur.archlinux.org/gnome-browser-connector.git
	cd gnome-browser-connector
	makepkg -si

	now find the Tray Icons Reloaded and install:
	https://extensions.gnome.org/extension/2890/tray-icons-reloaded/

# Manage an existing extra SSD like a data SSD

	sudo groupadd data
	sudo usermod -a -G data $USER
	
	sudo chown -R $USER . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	sudo chgrp -R data . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	
	Get the media path on:
	ls -la /run/media/$USER/

# SSH
	sudo pacman -S xclip

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"
	ls -la ~/.ssh
	eval "$(ssh-agent -s)"
	ssh-add ~/.ssh/id_ed25519

	xclip -sel clip < ~/.ssh/id_ed25519.pub

# Sensors and System Monitor

	sudo sensors-detect
	sensors
	sudo pacman -S psensor
	
	sudo pacman -S bashtop
	sudo pacman -S htop
	sudo pacman -S powertop
	
# Trim auto service (to SSD's and nvme's)

	*Continuous trim is not recommended by UNIX community
	
	Check if its enabled (you should read *Active: active (waiting)*)
	systemctl status fstrim.timer
	
	If not, enable it:

	sudo systemctl enable fstrim.timer
	reboot
	
# DSP LADSPA Plugin (for audio correction)

	pacaur -S jamesdsp
	
	DSP for Windows: https://sourceforge.net/p/equalizerapo/wiki/Documentation/
	Download on: https://www.roomeqwizard.com/

	Then, download some corrections files like: http://noaudiophile.com/Logitech_z623/Logitech_z623.txt
		
# JVM

	#sudo pacman -S jdk-openjdk
	sudo pacman -S jdk17-openjdk
	#sudo archlinux-java set java-17-openjdk
	archlinux-java status
	
	sudo pacman -S visualvm
	sudo pacman -S scala	# scala -version
	sudo pacman -S maven 	# mvn --version
	sudo pacman -S sbt
	sbt --version 			# need to run and wait for downloads
	
	sudo pacman -S intellij-idea-community-edition

# Working and Office
	
	sudo pacman -S exfat-utils
	sudo pacman -S ntfs-3g
	
	pacaur -S discord-canary
	pacaur -S teams
	pacaur -S zoom
	pacaur -S slack-desktop
	
	pacaur -S freeoffice
	pacaur -S krita (image editor)
	pacaur -S rhythmbox (audio player)
	pacaur -S audacity (audio editor)
	pacaur -S kdenlive (video editor)
	pacaur -S exif (image metadata editor)
	
	sudo pacman -S steam
	
# Spotify (native for linux)

	Sign the key and install:
	curl -sS https://download.spotify.com/debian/pubkey.gpg | gpg --import -
	pacaur -S spotify
	
# Docker 

	sudo pacman -S docker
	sudo usermod -aG docker $USER
	sudo systemctl start docker 
	sudo systemctl enable docker 
	sudo docker run hello-world 

	If you have nvidia GPU, for docker:

	pacaur -S nvidia-container-runtime
	reboot system
	docker run -it --rm --gpus all ubuntu nvidia-smi

# .NET 5

	sudo pacman -S dotnet-sdk
	sudo pacman -S aspnet-runtime

	dotnet --info
	dotnet a+TAB (*autocomplete)
	dotnet +TAB (*autocomplete)

	Security problems:
	dotnet dev-certs https --trust (*did you read the "A valid HTTPS certificate is already present." message?, great!)

# Visual Studio code, ide for C# (native version):

	pacaur -S visual-studio-code-bin 
	open it
	CTRL+P
	ext install ms-dotnettools.csharp
	ext install EditorConfig.EditorConfig

	Best project tutorial MANUAL start ever!
	https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-nunit

Python3 + Pypy3

	*Create the default venv python directory, and test it:
	python3 -m venv ~/.venv/python3
	source ~/.venv/python3/bin/activate
	pip list
	deactivate

	*Install the PyPy3, VM with Jit + performance:
	sudo pacman -S pypy3
	pypy3 -m venv ~/.venv/pypy3
	source ~/.venv/pypy3/bin/activate
	pip list
	deactivate

	sudo pacman -S pycharm-community-edition

# For dual boot with Windows, OS needs to be adjusted to UTC. On Windows run this command:

	reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
	
# After install checklist

	Clean pacman cache:
	sudo pacman -Sc
	
	List/ Remove Orphans:
	pacman -Qtdq
	sudo pacman -R ....
	
	Allocated SSD space:
	df
	sudo du -h --max-depth=1 /
	
# How to add user as superuser on sudoers

	su
	*password
	visudo /etc/sudoers
	include on it:
	joao ALL=(ALL) ALL
	
	reboot
	
# How to VPN 

	You can add the file.ovpn on Settings/Network or using the terminal:

	sudo nmcli connection import type openvpn file /path/to/your.ovpn
	nmcli connection up <connection-name>
	nmcli connection show

	Use the default gateway if your VPN host do not have access to external network:

	sudo ip route add default via <default-route>

	hint: Get your Default Route on *Settings/Network/Wired-or-WiFi/settings/Details/Default Route*
	example: sudo ip route add default via 192.168.15.1
	
# How to run script with systemclt (systemd controller). Example:
	
	sudo touch /usr/local/sbin/mytask.sh
	sudo chmod +x /usr/local/sbin/mytask.sh
	sudo vim /usr/local/sbin/mytask.sh
	"""
	the shell script here
	"""
	
	sudo touch /etc/systemd/system/myactivator.service
	sudo chmod +x /etc/systemd/system/myactivator.service
	sudo vim /etc/systemd/system/myactivator.service
	"""
	[Unit]
	Description=My Task

	[Service]
	ExecStart=/usr/local/sbin/mytask.sh

	[Install]
	WantedBy=multi.user.target
	"""
	
	sudo systemctl enable myactivator.service
	
	reboot

# How to rescue the pacman keys fucked up:	

	Check files:
	ls -la /usr/share/pacman/keyrings
	ls -la /etc/pacman.d/gnupg
	ls -la /var/lib/pacman/sync/

	Try:
	sudo rm -r /etc/pacman.d/gnupg
	sudo pacman-key --init
	sudo pacman-key --populate archlinux 

