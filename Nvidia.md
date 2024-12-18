# NVIDIA proprietary driver manual install 

	get the file (can download from nvidia website)
	stop xorg server (infertace): 
	systemctl stop gdm
	Ctrl+Alt+F3: Opens TTY 3
	sudo sh NVIDIA-Linux-driver.run

	*it will tell you that there is a nouveou configured on kernel and will not proceed
	press OK
	*then it will say about auto generate and write a .config file on kernel to disable it...
	Allow it to try and pray!
	reboot
	
	sudo sh NVIDIA-Linux-driver.run
	*if install runs, it will ask you about register kernel module sources
	YES
	
# IF NVIDIA NO VIDEO BECAUSE OF F*** NEW DRIVER

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
	goto nvidia-settings Configuration: Save Current Configuration
 	save on home: .nvidia-settings-rc

  	
   	Install nfancurve, if it breaks because "nvidia-settings" use -e:
    	pacaur -S nfancurve -d
     	pacaur -S nfancurve -d -e
      	*** on edit remove the line: "depends=("bash" "nvidia-utils" "nvidia-settings" "procps")"
       :wq
  
 	sudo vim /etc/nfancurve.conf (CTRL + SHIFT + V to paste)
		min_t="50"
		sleep_time="1"
		hyst="2"
		fcurve="25 26 27 28 30 32 34 36 38 40 43 46 50 54 58 62 66 70 75 80 85 90 95 99" # fan speeds
		tcurve="51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74" # temperatures
		force_check="0"
		which_curve="1 1 1 1"
  		default_fan="0"
    		fan2gpu="0 0 1 1"
  	:wq

	nfancurve -l -c /etc/nfancurve.conf

	Finally, enable as a service:
	
		systemctl --user daemon-reload
		systemctl --user start nfancurve.service
		systemctl --user enable nfancurve.service

	reboot
