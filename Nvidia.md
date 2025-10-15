# NVIDIA driver install 

https://wiki.archlinux.org/title/NVIDIA#

# Nvidia fan control (nfancurve) 

   	Install nfancurve, if it breaks because "nvidia-settings" use -e:
    	pacaur -S nfancurve -d
     	pacaur -S nfancurve -d -e
      	*remove the line: "depends=("bash" "nvidia-utils" "nvidia-settings" "procps")"
       :wq

  	* for MSI TRIO RTX 2080TI with 2 PWM: 
 	sudo vim /etc/nfancurve.conf (CTRL + SHIFT + V to paste)
  
		min_t="50"
  		min_t2="50"
    
		sleep_time="3"
  
		hyst="2"
  
		fcurve="25 26 27 28 30 32 34 36 38 40 43 46 50 54 58 62 66 70 75 80 85 90 95 99" # fan speeds
		tcurve="51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74" # temperatures
  
  		force_check="0"
    
 		fcurve2="25 26 27 28 30 32 34 36 38 40 43 46 50 54 58 62 66 70 75 80 85 90 95 99" # fan speeds
		tcurve2="51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74" # temperatures 		
		
		which_curve="1 2"
  
		default_fan="0"
  
		fan2gpu="0 0"
 
	:wq

	*test it
	sudo nfancurve -l -c /etc/nfancurve.conf

	Finally, enable as a service:
	
		systemctl --user daemon-reload
		systemctl --user start nfancurve.service
		systemctl --user enable nfancurve.service

	reboot
