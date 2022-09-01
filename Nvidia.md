# NVIDIA proprietary driver manual install 

	get the file (can download from nvidia website)
	stop xorg server (infertace): 
	systemctl stop gdm
	Ctrl+Alt+F3: Opens TTY 3
	sudo sh NVIDIA-Linux-x86_64-470.129.06.run

	*it will tell you that there is a nouveou configured on kernel and will not proceed
	press OK
	*then it will say about auto generate and write a .config file on kernel to disable it...
	Allow it to try and pray!
	reboot
	
	sudo sh NVIDIA-Linux-x86_64-470.129.06.run
	*if install runs, it will ask you about register kernel module sources
	YES
	
* IF NVIDIA NO VIDEO BECAUSE OF F*** NEW DRIVER

	after fresh install, if you have old nvidia and forgot to use nouveou
	Ctrl+Alt+F3: Opens TTY 3
	su
	password
	pacman -R nvidia-dkms
	pacman -R nvidia-utils

	reboot (should enter on graphics interface now using nouveou)

# Nvidia fan control (nfancurve)

	sudo nvidia-settings
	goto GPU0: Thermal Settings/Enable GPU Fan Settings
	goto X Server Display Configuration: Save to X Configuration File
	save on (default): /etc/X11/xorg.conf
	
	sudo vim /etc/X11/xorg.conf
	
		edit the "Device" section (add the Option Coolbits line as is below):
		
			Section "Device"
				Identifier     "Device0"
		>>>>>>>>	Option         "Coolbits" "12"
				Driver         "nvidia"
				VendorName     "NVIDIA Corporation"
			EndSection
		
	pacaur -S nfancurve -d (probably will break)

	* If it breaks because "nvidia-settings" follow the next steps, try this:
	
		pacaur -S nfancurve -d -e	

		on edit remove the line:
			depends=("bash" "nvidia-utils" "nvidia-settings" "procps")
			
		:wq

	sudo vim /etc/nfancurve.conf

		min_t="25" (if your GPU supports zero speed fans go '0')
		sleep_time="2" (more responsive fans)

		fcurve="25 28 31 34 37 40 43 46 49 52 56 60 64 68 72 76 80 84 88 92 96 99 99 99" # fan speeds
		tcurve="40 44 48 52 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 80 90" # temperatures

		If your GPU has two PWM fan, set it or use the same profile on which_curve param:
		which_curve="1 1 1 2"

	nfancurve -l -c /etc/nfancurve.conf

	Finally, enable as a service:
	
		systemctl --user daemon-reload
		systemctl --user start nfancurve.service
		systemctl --user enable nfancurve.service

	reboot
