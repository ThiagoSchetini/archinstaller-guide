ARCH INSTALLER GUIDED GUIDE FOR A WORKSTATION (OMG!) 
====================================================
	
Find and create your ISO on: https://archlinux.org/download/

This reading is for `GNOME` interface mostly.

Before start the installer, do a manual partition clean
-------------------------------------------------------

The recent versions of archinstaller deals with this, but, i found it nice manually doing a cleaning on partitions.

Before boot, wait for your ISO's image terminal open.

Remove all partitions of the destination disk:

	parted
	print all 
	select "/dev/nvme0n1 ... or the correct SSD that you will use"
	print "the partitions"
	rm 1
	rm 2
	...
		
Prepare the partition table:

	parted mktable GPT
	
Configuring the installer
-------------------------

1 Start it:
	
	archinstall
	
2 Select a mirror near from you, ex: Brazil
	
3 Do yourself the partitions (It's good for advanced users, but if you are not, the automatic best efforts would be great):
	
> Create a partition for boot as `Fat32` starts at `5243kB` ends at `513MB`

> Create another partition for system as `Ext4` starts at `518MB` ends at `100%`

> Mount the first to `/boot`

> Mount the second to `/`

4 Confirm `systemd` as boot (modern one)
	
5 No swap if you have 32GB+ RAM `let it crash :)`
	
6 Hostname is the machine name, like "WorkstationX"
	
7 Do NOT use password on sudo, you should create a sudo user. Potential risk: https://wiki.archlinux.org/title/Sudo#Disable_root_login
	
8 Create at least one user for you like "joao" and mark it as a superuser
	
9 On selecting profile desktop choose the correct GPU driver like proprietary for most of nvidia cards
		
10 Confirm `pipewire` as audio server (most modern one)
	
11 For most workstations, mark only one kernel `linux-zen` (+performance, +power, -throughput). You can read more about linux-zen kernel improvements on https://liquorix.net/
		
12 Put aditional packages like `firefox`
	
13 On Network, choose `NetworkManager`
		
14 On Timezone choose the right one for you like `America/Sao_Paulo`
	
15 On Optional Repositories do not forget to mark `multilib` for most workstations
	
16 **Run the installer** and when finish:

	reboot
	
After install
-------------
	
If you forgot multilib, enable it manually. Uncoment the multilib session from your pacman, both lines:
	
	sudo vim /etc/pacman.conf

	[multilib]
	Include = /etc/pacman.d/mirrorlist

	GOTO Utils.md (on this repository) to Mirrorlist (generate and replace with better and more complete one)

GOTO Utils.md run a packman keys update

VIM as default editor:
	
	su
	**password**
	
	vim /etc/environment 
	**include on it**
	EDITOR=/usr/bin/vim	
	
	reboot

GOTO Utils.md to set the boot timeout and the default kernel, if you installed two or more!
	
Source/devel zen:

	sudo pacman -S linux-zen-headers
 	sudo pacman -S base-devel
  	sudo pacman -S cppcheck

GIT:

	sudo pacman -S git
	mkdir ~/Repositories
	
YAY install/refresh:
	
 	check base-devel installed (sudo pacman -S base-devel)

	git clone https://aur.archlinux.org/yay.git
	cd yay
	makepkg -si

	cd ..
	rm -r yay
	
Gnome System Tray (to use apps on background like discord)

	sudo pacman -S gnome-browser-connector

	**now find the Tray Icons Reloaded and install**
	https://extensions.gnome.org/extension/2890/tray-icons-reloaded/

Sensors and System Monitor on Tray

	sudo sensors-detect
	**put yes on all of them, no problems**
	
	sensors

	sudo pacman -S libappindicator-gtk3

 	goto: https://extensions.gnome.org/local/
	+ AppsMenu
  	+ Status Icons

	https://github.com/chinf/psensor
	./configure;make clean all
	sudo make install
 	**open and config psensor to start on boot**
 
	sudo pacman -S bashtop
	
Trim auto service check (to SSD's and nvme's):

	**continuous trim is not recommended by UNIX community**
	
	**check if its enabled (you should read *Active: active (waiting)*)**
	systemctl status fstrim.timer
	
	**if not, enable it**
	sudo systemctl enable fstrim.timer
	
	reboot
	
DSP LADSPA Plugin (for audio correction)

	pacaur -S jamesdsp

	**Then, download some corrections files like: http://noaudiophile.com/Logitech_z623/Logitech_z623.txt**
		
JVM & Data Engineering Coding

	sudo pacman -S jdk-openjdk (last one)

	sudo pacman -S jdk8-openjdk
 	sudo pacman -S jdk11-openjdk
	sudo pacman -S jdk17-openjdk
 	sudo pacman -S jdk21-openjdk
  	sudo pacman -S jdk23-openjdk

	sudo archlinux-java set java-11-openjdk
	archlinux-java status
	
	sudo pacman -S visualvm
	
	sudo pacman -S scala
	scala -version
	
	sudo pacman -S maven
	mvn --version
	
	sudo pacman -S sbt
	sbt --version
	
	sudo pacman -S intellij-idea-community-edition
 	sudo pacman -S pycharm-community-edition
  	yay -S visual-studio-code-bin (https://wiki.archlinux.org/title/Visual_Studio_Code)
   	sudo pacman -S dbeaver

Docker 

	sudo pacman -S docker
	sudo usermod -aG docker $USER
	sudo systemctl start docker 
	sudo systemctl enable docker 
	sudo docker run hello-world 

Working, Audio, Images, Video
	
	sudo pacman -S exfat-utils
	sudo pacman -S ntfs-3g

 	sudo pacman -S firefox
  	sudo pacman -S google-chrome
   	yay -S freeoffice
   
	yay -S rhythmbox (audio player)
 	sudo pacman -S spotify-launcher
	
 	sudo pacman -S openrgb
	sudo pacman -S steam

CLEAN

	pacman -Qe (search for not used installed default dependencies carefully)
	GOTO Utils.md run the pacman clean instructions
