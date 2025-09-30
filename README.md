# Repository for using the Raspberry Pi 5 with a Sense HAT, enabling communication with PlayGround: receiving signals from PlayGround and sending inputs from the Sense HAT joystick.

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
If the connection is successful, the Python script will be able to receive Playground values and change the sensehat' status accordingly.


## Wiring

Unlike other projects that required complicated wiring this project only need you to attach the hat to gpio pin.

# Knowledge required before continuing.

## 1. What is the Vehicle Signal Specification (VSS)?
In a modern vehicle, hundreds of electronic control units (ECUs) and sensors are constantly generating data. This includes everything from the vehicle's speed and engine RPM to the status of lights, doors, and infotainment systems.

Historically, accessing this data has been complex, with each manufacturer using proprietary formats and protocols. The Vehicle Signal Specification (VSS), a project hosted by the COVESA Alliance, solves this problem by creating a standardized, common language for describing vehicle data.

VSS defines a structured tree of signals, providing a canonical way to name and organize data. For example:

- Vehicle.Speed always refers to the vehicle's current speed.
- Vehicle.Powertrain.FuelSystem.Level refers to the fuel tank level.
- Vehicle.Cabin.Door.FrontLeft.IsLocked refers to the lock status of the driver's door.

By using this standardized tree, developers can write applications that are portable across different vehicle makes and models, without needing to worry about the underlying hardware differences.

## 2. The Role of a Data Broker
If VSS is the language, then a Data Broker is the central hub or "librarian" that manages all the data. In our project, we use Eclipse Kuksa.val as our data broker.

The data broker is responsible for:

- Storing the current value of all vehicle signals defined by VSS.
- Providing access for other applications (like our future Python script) to read or write signal values.
- Enforcing security to ensure that only authorized applications can modify sensitive data (like unlocking doors).
- Notifying applications when a signal value they are interested in has changed.
- Applications communicate with the Kuksa.val data broker using protocols like gRPC or WebSockets, which is what our kuksa-client library will do in the next course.

## 3. What is sdv-runtime?
Setting up a full data broker, VSS database, and all the required services from scratch can be complicated. This is where sdv-runtime comes in.

sdv-runtime is a pre-packaged environment, delivered as a Docker container, that includes everything you need to get started with SDV development. When you run it, it automatically starts:

A Kuksa.val Data Broker instance.
A pre-loaded VSS database with a wide range of standard signals.
Services that allow it to connect to cloud platforms like playground.digital.auto.

<img width="937" height="778" alt="image" src="https://github.com/user-attachments/assets/17f21899-d4bc-4954-9921-948313bc07f6" />

Using sdv-runtime saves us from a complex setup process and lets us focus on building our application. It provides a ready-to-use VSS data layer for our project.

In the next lesson, we will install Docker on our Raspberry Pi and deploy the sdv-runtime container.

## 4. What is playground.digital.auto?
playground.digital.auto is a web-based platform for prototyping and validating Software-Defined Vehicle (SDV) applications. It provides a virtual, 3D-rendered vehicle model and a dashboard of controls that are connected to a VSS-compliant data broker in the cloud.

Crucially, sdv-runtime (which you have running on your Pi) has built-in capabilities to connect to the Playground. When connected, the data broker on your Pi and the data broker in the Playground's cloud become synchronized.

This means:

- A change made on your physical joystick will be sent to your local data broker, which then forwards it to the Playground, updating the cloud dashboard.
- A change made in the Playground's web dashboard will be sent to your local data broker, which our script then uses to update the physical Sense HAT.
This completes the entire end-to-end data loop, from a web browser, through the cloud, to your local hardware, and back.

For more information about playground: *https://www.youtube.com/watch?v=HQrsGY7XLU4&t=9s*


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
If there is nothing wrong you should able to control sensehat's light using api on PlayGround or natively control using joystick.

### Using Guide:

#### Joystick supoort:
For local testing and debug, developer have added features for configuring sensehat led using joystick
- Pressed: for turning off
- Left: for changing color
- Up: for increasing intensity
- Down: for decreasing intensity

The state of sense hat that you just changed using joystick will be automatically update to playground




