# This is the repository for running pi 5 with sense hat with ability to receive signal from PlayGround

## Hardware Requirements

- Raspberry Pi 5
- Sense Hat

## Software Requirements

- Thonny , VScode or similar IDE
- Libraries:
  - `kuksa_client`
  - `sense_hat`
  - `docker`


## Workflow

To enable control of sensehat through Playground, the Raspberry Pi 5 needs to run Docker for `sdv-runtime`, which acts as a bridge between the Pi 5 and Playground.  
If the connection is successful, the Python script will be able to receive Playground values and change the NeoPixels' status accordingly.


## Wiring

Unlike other projects that required complicated wiring this project only need you to attach the hat to gpio pin.


## Step-by-Step Guide

### Step 1: Run the sdv-runtime natively on Pi 5

To start the SDV runtime, use the following command:

<pre>  docker run -it --rm -e RUNTIME_NAME="KKK" -p 55555:55555 --name sdv-runtime ghcr.io/eclipse-autowrx/sdv-runtime:latest  </pre>

### Step 2: create a virtual environment by executing.

<pre>  
#create virtual enviroment         
python3 -m venv venv               

#activate the virtual environment  
source ./venv/bin/activate         
</pre>

Then install all the dependencies:

<pre>
# Upgrade pip first 
pip3 install --upgrade pip 

# Install kuksa_client (may require git or custom installation if not on PyPI) 
pip3 install kuksa-client 
  
# Install Adafruit Blinka to get 'board' module 
sudo apt-get install sense-hat
  
</pre>

### Step 4: run file **playground_sense.py**
**Warining**: If you just start docker and go straight into running the code. As current value is now None, just like every other value. Consider, changing ambient on, color and Intensity.
If there is nothing wrong you should able to control sensehat's light using api on PlayGround.

### Using Guide:

#### Joystick supoort:
For local testing and debug, developer have added features for configuring sensehat led using joystick
- Pressed: for turning off
- Left: for changing color
- Up: for increasing intensity
- Down: for decreasing intensity

The state of sense hat that you just changed using joystick will be automatically update to playground




