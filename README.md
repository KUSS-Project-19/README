# Getting Started with Example Settings


## Server (Soviet) Set Up

1) https://drive.google.com/open?id=1X6AiJvukzd6MyB2VWICH0IMoIDV6a63u Download virtual box image
1) Start virtual machine
1) id: ikh, pwd: security
1) `cd soviet`
1) `git pull`
1) `npm install`
1) `npm start`
1) Open https://localhost:9001/ with browser (note: do not worry about faulty certificate)
1) Log in (user id: 1234, pwd: 1234)

## Device Client (Fascio) Set Up

1) Install Node.js ([https://nodejs.org/ko/download/](https://nodejs.org/ko/download/))
1) `git clone https://github.com/KUSS-Project-19/fascio`
1) copy ./etc/settings.json.example to a new file ./etc/settings.json
1) `npm install`
1) `./test.bat`
1) `npm start`

## How to use Example
(☞ﾟヮﾟ)☞ ☜(ﾟヮﾟ☜)

**Congratulations! You can now run the example IoT server-device model.**

**The example IoT server-device model has 1 user who owns 1 device, both Node.js**

**The example (virtual) IoT device sends sensor data to the server every 5 seconds**

**You can send an output action to the device by pushing the "Do Something" button**



# Toy Model

## Scenario

Our IoT toy model assumes the following scenario:

1) The server (soviet) is owned by an IoT service company.
1) The server has a DB that contains the information of all users and devices (fasci).
1) There may be an arbitrary number of users and an arbitrary number of devices.
1) The IoT service company also produces and sells IoT devices.
1) Each device (fascio) has a unique device ID.
1) Each device also has a password.
1) Each device is equipped with 1 sensor and 1 output(ex. an LED, etc.).
1) Each device regularly sends sensor data to the server.
1) Upon the production of a new device, it's device ID and device password is added to the server's DB by the IoT service company directly.
1) A user may order output actions on the output of the device.
1) A user is provided with the device ID and device password through secure analogue means upon purchase of a device.
1) The purchase of a device/devices by a user is assumed to happen through secure analogue means.
1) A user, when creating an account on the server, **must** also register to that account at least 1 valid device using the previously acquired device ID and device password.
1) A user may purchase and register additional devices, thus owning a bundle of devices.
1) The server URL is assumed to be publicly available.

## Architecture

### Server (Soviet)

* Server (soviet) is Node.js(npm).
* It also has a MySQL DB.
* DB contains user and device information (see soviet/sqlscripts/soviet.sql).
* The Bcrypt has of the user password is stored in the DB.
* DB also stores all sensor data.
* The server updates the DB upon user registration or device production.
* The server provides information in the DB to the user.
* The server settings must specify the open server port number, session name and password, DB host, DB username, DB password and DB name.
* The server must be named soviet.

### Device (Fascio)

* A device can run on any hardware as long as it has a sensor and an output functionary.
* A device rejects all inbound attempts at establishing a connection, and upon being turned on will continuously attempt to establish a duplex connection with the server until successful.
* Once successful connection is established with the server, the device will maintain the same connection/session until the device is turned off.
* A device can only be turned on and off directly through manual, analogue means. The server cannot turn the device off.
* Device settings must specify a device ID, device password, and the location(URL) of the server.
* As long as these conditions are satisfied, a device need not be Node.js as in the example code.

### User

* Accesses the server using a browser.
* A user may request account creation.
* A user may request sign in.
* A user may request to register a newly purchased device.
* A user may order an output action to a registered device.
* A user may request to view the sensor logs for a registered device.

# Set Up (Actually Getting Started)

## Soviet Set Up

1) Install npm, Node.js, MySQL, and nginx.
1) To desired directory: git clone https://github.com/KUSS-Project-19/soviet.git
1) Modify ./etc/settings.json to desired specifications
	*
1) Modify ./sqlscripts/soviet.sql to desired specifications
	*
1) `npm install`
1) `npm start`

## Fascio Set Up
