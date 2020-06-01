# Corsair Hydro X Water Temp for Linux

Earlier this year (2020) I built my first entirely water cooled AMD Ryzen system as my daily driver. I dual boot this system, primarily with Linux for work/general-purpose, and Windows for gaming. This presented me with an issue I didn't initially consider - the Corsair iCUE software only works on Windows, and so I had no water temperature management on Linux.

Thankfully, I stumbled across [OpenCorsairLink](https://github.com/audiohacked/OpenCorsairLink) which became the base of this project (thank you guys). As I have a rather unique multi Commander Pro setup, I needed to write my own script that interfaced with OpenCorsairLink to set the fan/pump speed based on the water temperature it was sensing. I'm putting it here just incase it becomes useful for others doing something similar.



> **NOTE**: The code in this repository is **not** part of any official Corsair software, nor is it sanctioned or approved by Corsair. The code is provided as-is, with no warranty or guarantees, in the hope that it will be useful to you; your mileage may vary and you may need to tweak to suit your individual environment. Use this at your own risk - I will not take any responsibility for any damage caused by overheating components.



## Installation

To get this up and running, you need to have [OpenCorsairLink](https://github.com/audiohacked/OpenCorsairLink) installed and running, as well as access to **zsh**. When ready, follow these instructions-

~~~bash
$ git clone https://github.com/rdoxenham/hydrox-linux
(...)

$ cd hydrox-linux
$ sudo cp corsair-temps /usr/bin/
$ sudo chmod +x /usr/bin/corsair-temps
$ sudo cp corsair-temps.service /etc/systemd/system/
~~~



By default, the script operates on a sliding scale - increasing both the pump speed and the fan speed based on the loop temperature itself - but if it's less than 30°C, then keep the fans/pump quiet. You will need to tweak the script to accommodate the fan/pump layout to suit your setup. In my environment I have two Commander Pro devices - one setup with 6 fans, and the other with a single fan and the HydroX pump and water temperature probe. To discover your layout use the following-

~~~bash
$ sudo /usr/share/OpenCorsairLink/OpenCorsairLink.elf --device 0
Dev=0, CorsairLink Device Found: Commander PRO!
Dev=1, CorsairLink Device Found: Commander PRO!

Vendor: Corsair
Product: Commander PRO
Firmware: V0.9.212
Temperature 0: 33.53 C
Temperature 1:  0.00 C
Temperature 2:  0.00 C
Temperature 3:  0.00 C
Output 12v: 12.19 V
Output 5v:  4.88 V
Output 3.3v:  3.32 V
Fan 0:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 852
Fan 1:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 0
Fan 2:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 0
Fan 3:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 0
Fan 4:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 0
Fan 5:	Mode: Auto/Disconnected
	PWM: 0%
	RPM: 3208
~~~



If you need to adjust, simply modify the `/usr/bin/corsair-temps` script to suit your requirements, i.e. fan ports, pump ports, and which device the temperature probe is on. Then, you can test with-

~~~bash
$ sudo /usr/bin/corsair-temps
Current Water temperature is: 33.56C
Current Fan RPM is: 856
Current Pump RPM is: 3356

(Ctrl-C to stop)
~~~

This script will continue to run until killed, and every 10 seconds it will update based on the most recent water temperature probe, so you can see it in action and should hear it if you load the system up to warm the loop. If you're happy with it then you can simply *Ctrl-C* the script and have it enabled with systemd:

~~~bash
$ sudo systemctl enable --now corsair-temps
Created symlink /etc/systemd/system/default.target.wants/corsair-temps.service → /etc/systemd/system/corsair-temps.service.
~~~



Any questions/problems please do let me know or raise an issue. Thanks and good luck!
