ARCH INSTALLER GUIDED GUIDE FOR A WORKSTATION (OMG!) 
====================================================
	
Find and create your ISO on: `https://archlinux.org/download/`

**This reading is for GNOME interface mostly**

Before start the installer
--------------------------

Wait for your ISO's image terminal open

Remove all partitions of the destination disk (archinstall can't do it):

	parted
	print all 
	select "/dev/nvme0n1 ... or the correct SSD that you will use"
	print "the partitions"
	rm 1
	rm 2
	...
		
Prepare the partition table (archinstall can't do it):

	parted mktable GPT
	
Configuring the installer
------------------------

Start it:
	
	archinstall
	
Select a mirror near from you, ex: Brazil
	
Do yourself the partitions:
	
> Create a partition for boot as `Fat32` starts at `5243kB` ends at `513MB`
> Create another partition for system as `Ext4` starts at `518MB` ends at `100%`
> Mount the first to `/boot`
> Mount the second to `/`

Confirm systemd as boot (modern one)
	
No swap if you have 32GB+ RAM `let it crash :)`
	
Hostname is the machine name, like "WorkstationX"
	
Put password on sudo please!
	
Create at least one user for you like "joao" and mark it as a superuser
	
On selecting profile desktop choose the correct GPU driver like proprietary for most of nvidia cards
		
Confirm pipewire as audio server (modern one)
	
For most workstations, mark only one kernel `linux-zen` (+performance, +power, -throughput), check on `https://liquorix.net/`
		
Put aditional packages like `firefox`
	
On Network, choose NetworkManager
		
On Timezone choose the right one for you like America/Sao_Paulo
	
On Optional Repositories do not forget to mark `multilib` for most workstations
	
**Run the installer** and when finish:

	reboot
	
After install
-------------
	
If you forgot multilib, enable it manually. Uncoment the multilib session from your pacman, both lines:
	
	sudo vim /etc/pacman.conf

	[multilib]
	Include = /etc/pacman.d/mirrorlist

Pacman keys update

	sudo pacman -Syy
	sudo pacman-key --refresh-keys

VIM as default editor
	
	su
	*password
	
	vim /etc/environment 
	include on it:
	EDITOR=/usr/bin/vim	
	
	reboot

Set the boot timeout and the default kernel, if you installed two or more:

	ls /boot/loader/entries
	*get the name of file (non mutable part)

	sudo vim /boot/loader/loader.conf
	timeout 3
	default *_linux-zen*
	
Source/devel zen:

	sudo pacman -S linux-zen-headers

GIT:

	sudo pacman -S git
	mkdir ~/Repositories
	
Pacaur:

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
	
For gnome, use Gnome System Tray (to use apps on background like discord)

	cd Repository
	git clone https://aur.archlinux.org/gnome-browser-connector.git
	cd gnome-browser-connector
	makepkg -si

	now find the Tray Icons Reloaded and install:
	https://extensions.gnome.org/extension/2890/tray-icons-reloaded/

Manage an existing extra SSD like a data SSD, here is my example:

	sudo groupadd data
	sudo usermod -a -G data $USER
	
	sudo chown -R $USER . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	sudo chgrp -R data . .. Books/ Docs/ Music/ 'Photo&Video'/ TODO/ Workstations/
	
	Get the media path on:
	ls -la /run/media/$USER/

Create one SSH public/private key for your workstation:

	sudo pacman -S xclip

	ssh-keygen -o -a 300 -t ed25519 -f ~/.ssh/id_ed25519 -C "MY-MACHINE"
	ls -la ~/.ssh
	eval "$(ssh-agent -s)"
	ssh-add ~/.ssh/id_ed25519

Copy the SSH public key (to memory) to use on external services like github, gitlab:

	xclip -sel clip < ~/.ssh/id_ed25519.pub

Sensors and System Monitor

	sudo sensors-detect
	**put yes on all of them, no problems**
	
	sensors
	
	sudo pacman -S psensor
	sudo pacman -S bashtop
	sudo pacman -S htop
	sudo pacman -S powertop
	
	**open and config psensor to start on boot**
	
Trim auto service check (to SSD's and nvme's):

	**Continuous trim is not recommended by UNIX community**
	
	Check if its enabled (you should read *Active: active (waiting)*)
	systemctl status fstrim.timer
	
	If not, enable it:
	sudo systemctl enable fstrim.timer
	
	reboot
	
DSP LADSPA Plugin (for audio correction)

	pacaur -S jamesdsp

	Then, download some corrections files like: http://noaudiophile.com/Logitech_z623/Logitech_z623.txt
		
JVM

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

Working and Office
	
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
	
Spotify (native for linux)

	Sign the key and install:
	curl -sS https://download.spotify.com/debian/pubkey.gpg | gpg --import -
	pacaur -S spotify
	
Docker 

	sudo pacman -S docker
	sudo usermod -aG docker $USER
	sudo systemctl start docker 
	sudo systemctl enable docker 
	sudo docker run hello-world 

	If you have nvidia GPU, for docker:

	pacaur -S nvidia-container-runtime
	reboot system
	docker run -it --rm --gpus all ubuntu nvidia-smi

.NET 5

	sudo pacman -S dotnet-sdk
	sudo pacman -S aspnet-runtime

	dotnet --info
	dotnet a+TAB (*autocomplete)
	dotnet +TAB (*autocomplete)

	Security problems:
	dotnet dev-certs https --trust (*did you read the "A valid HTTPS certificate is already present." message?, great!)

Visual Studio code, ide for C# (native version):

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

For dual boot with Windows, OS needs to be adjusted to UTC. On Windows run this command:

	reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
	
After install make some clean:

	Clean pacman cache:
	sudo pacman -Sc
	
	List Orphans:
	pacman -Qtdq
	
	Remove the Orphans
	sudo pacman -R aaa bbb ccc
	
	Do it again because there will be more orphans:
	pacman -Qtdq
	
Tips
----
	
Check allocated SSD space:

	df
	sudo du -h --max-depth=1 /
	
How to rescue the pacman keys fucked up:	

	Check files:
	ls -la /usr/share/pacman/keyrings
	ls -la /etc/pacman.d/gnupg
	ls -la /var/lib/pacman/sync/

	Try:
	sudo rm -r /etc/pacman.d/gnupg
	sudo pacman-key --init
	sudo pacman-key --populate archlinux 
	
How to add user as superuser on sudoers

	su
	*password
	visudo /etc/sudoers
	include on it:
	joao ALL=(ALL) ALL
	
	reboot
	
VPN 

	You can add the file.ovpn on Settings/Network or using the terminal:

	sudo nmcli connection import type openvpn file /path/to/your.ovpn
	nmcli connection up <connection-name>
	nmcli connection show

	Use the default gateway if your VPN host do not have access to external network:

	sudo ip route add default via <default-route>

	hint: Get your Default Route on *Settings/Network/Wired-or-WiFi/settings/Details/Default Route*
	example: sudo ip route add default via 192.168.15.1
	
How to run script with systemclt (systemd controller). Example:
	
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

