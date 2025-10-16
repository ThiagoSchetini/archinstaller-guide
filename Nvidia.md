# NVIDIA driver install 

https://wiki.archlinux.org/title/NVIDIA#

# NVIDIA fan control (nfancurve) 

### Install nfancurve

    yay -S nfancurve

If it breaks because "nvidia-settings" use -e:

	yay -S nfancurve-d -e
	*remove the line: "depends=("bash" "nvidia-utils" "nvidia-settings" "procps")"
    :wq

### Create the fan profile. 



Open the conf file:

	sudo vim /etc/nfancurve.conf 

Paste the content. Example for MSI TRIO RTX 2080TI with 2 PWM: 

	min_t="50"
	min_t2="50"
	
	sleep_time="3"
	
	hyst="2"
	
	fcurve="25 26 27 28 30 32 34 36 38 40 43 46 49 52 56 60 64 68 72 75 78 80 81 82 83 84 85 86 87 88 89 90" # fan speeds
	tcurve="51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82" # temperatures
	
	force_check="0"

	fcurve2="25 26 27 28 30 32 34 36 38 40 43 46 49 52 56 60 64 68 72 75 78 80 81 82 83 84 85 86 87 88 89 90" # fan speeds
	tcurve2="51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82" # temperatures
	
	which_curve="1 2"
	
	default_fan="0"
	
	fan2gpu="0 0"
 
### Test

	sudo nfancurve -l -c /etc/nfancurve.conf

### Enable as a system service

Check your user id:

	id -u

Create the system file first:

	sudo vim /etc/systemd/system/nfancurve.service

Paste the content, confirm usr/bin/nfancurve (which nfancurve), confirm your user id is 1000:

	[Unit]
	Description=NFanCurve (system-wide)
	After=multi-user.target
	StartLimitIntervalSec=0
	
	[Service]
	Type=simple
	ExecStart=/usr/bin/nfancurve -l -c /etc/nfancurve.conf
	Restart=always
	RestartSec=5
	User=root
	
	# Fix environment so nvidia-settings can talk to GPU
	Environment=DISPLAY=:0
	Environment=XDG_RUNTIME_DIR=/run/user/1000
	Environment=DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
	
	# Optional logging (you can check /tmp/nfancurve.log)
	StandardOutput=append:/tmp/nfancurve.log
	StandardError=append:/tmp/nfancurve.log
	
	[Install]
	WantedBy=multi-user.target

System service:

	sudo systemctl daemon-reload
	sudo systemctl enable --now nfancurve.service

Check service:

	systemctl status nfancurve.service
