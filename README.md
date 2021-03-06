# Fork informations
This is my fork of the "Smart Bartender" project because the original and already forked projects are not maintained anymore.
I changed a lot for my circumstances. 

- Added custom font InputSans
- Added text wrap to get more text displayed on the OLED display
- Added a shutdown function to not break the microSD card in any way

More incoming and regularily updated! If you want to add something. Do it. I maintain this repo normally. Thanks.

This fork is also compatible to python3 which is the standard python on modern raspberrypi instlalations.

Attention: By default the LED strip is deactivated. Just uncomment the line 110 to activate the normal LED function.

(Original readme from the forked origin project)
# Smart Bartender with Adafruit SSD1306

Make sure the following are installed:
* Python  (should already be installed on most Raspberry Pi)
* [pip](https://www.raspberrypi.org/documentation/linux/software/python.md)

### Enable SPI
You'll need to enable SPI for the OLED screen to work properly. Typing the following command in the terminal will bring you to a configuration menu.

```
raspi-config 
```

Then navigate to `Interfacing Options` and select `SPI`. Make sure it's turned on and reboot.

See this [article](https://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/) for more help if you need it.

### I2C
Make sure i2c is also configured properly. Type

```
sudo nano /etc/modules
```

in the terminal

press `i`, then make sure to paste the following two lines in the file:

```
i2c-bcm2708
i2c-dev
```

press `esc` then `ZZ` to save and exit.

## OLED Setup

You need to install the Adafruit Python SSD1306 library with

```
sudo python -m pip install --upgrade pip setuptools wheel
sudo pip install Adafruit-SSD1306
```

Or alternatively:

```
sudo python -m pip install --upgrade pip setuptools wheel
git clone https://github.com/adafruit/Adafruit_Python_SSD1306.git
cd Adafruit_Python_SSD1306
sudo python setup.py install
```

There is also a [guide](https://learn.adafruit.com/ssd1306-oled-displays-with-raspberry-pi-and-beaglebone-black/usage) on the Adafruit website if you get stuck.

## Running the Code

First, make sure to download this repository on your raspberry pi. Once you do, navigate to the downloaded folder in the terminal:

```
cd ~/path/to/directory
```

and install the dependencies

```
sudo pip install -r requirements.txt
```

You can start the bartender by running

```
sudo python bartender.py
```

### How it Works
There are two files that support the bartender.py file:

#### drinks.py
Holds all of the possible drink options. Drinks are filtered by the values in the pump configuration. If you want to add more drinks, add more entries to `drinks_list`. If you want to add more pump beverage options, add more entries to the `drink_options`.

`drinks_list` entries have the following format:

```
{
		"name": "Gin & Tonic",
		"ingredients": {
			"gin": 50,
			"tonic": 150
		}
	}
```

`name` specifies a name that will be displayed on the OLED menu. This name doesn't have to be unique, but it will help the user identify which drink has been selected. `ingredients` contains a map of beverage options that are available in `drink_options`. Each key represents a possible drink option. The value is the amount of liquid in mL. *Note: you might need a higher value for carbonated beverages since some of the CO2 might come out of solution while pumping the liquid.*

`drink_options` entries have the following format:

```
{"name": "Gin", "value": "gin"}
```

The name will be displayed on the pump configuration menu and the value will be assigned to the pump. The pump values will filter out drinks that the user can't make with the current pump configuration. 

### pump_config.json
The pump configuration persists information about pumps and the liquids that they are assigned to. An pump entry looks like this:

```
"pump_1": {
		"name": "Pump 1",
		"pin": 17, 
		"value": "gin"
	}
```

Each pump key needs to be unique. It is comprised of `name`, `pin`, and `value`. `name` is the display name shown to the user on the pump configuration menu, `pin` is the GPIO pin attached to the relay for that particular pump, and `value` is the current selected drink. `value` doesn't need to be set initially, but it will be changed once you select an option from the configuration menu.

Our bartender only has 6 pumps, but you could easily use more by adding more pump config entries.

### A Note on Cleaning
After you use the bartender, you'll want to flush out the pump tubes in order to avoid bacteria growth. There is an easy way to do this in the configuration menu. Hook all the tubes up to a water source, then navigate to `configure`->`clean` and press the select button. All pumps will turn on to flush the existing liquid from the tubes. I take the tubes out of the water source halfway through to remove all liquid from the pumps. *Note: make sure you have a glass under the funnel to catch the flushed out liquid.*


### Running at Startup
You can configure the bartender to run at startup by starting the program from the `rc.local` file. First, make sure to get the path to the repository directory by running

```
pwd
```

from the repository folder. Copy this to your clipboard.

Next, type

```
sudo nano /etc/rc.local
```

to open the rc.local file. Next, press `i` to edit. Before the last line, add the following two lines:

```
cd your/pwd/path/here
sudo python bartender.py &
```

`your/pwd/path/here` should be replaced with the path you copied above. `sudo python bartender.py &` starts the bartender program in the background. Finally, press `esc` then `ZZ` to save and exit. 

If that doesn't work, you can consult this [guide](https://www.dexterindustries.com/howto/run-a-program-on-your-raspberry-pi-at-startup/) for more options.
