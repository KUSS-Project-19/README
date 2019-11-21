# Getting Started with Example Settings

Example settings is just for *"hello world"* demonstration, not the real one.

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
1) The server has a DB that contains the information of all users and devices (fascio).
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

* Server is written in nodejs.
* A Nginx server receives inbound HTTPS connection, and passes to localhost an HTTP connection to the nodejs server program by [nginx reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/).
* Server rejects all inbound attempts except Nginx HTTPS connection (443) and SSH connection (22) by an [iptables](https://linux.die.net/man/8/iptables) firewall.
* Server requires a MySQL DB Server.
* DB contains user and device information (see soviet/sqlscripts/soviet.sql).
* The Bcrypt hash of the user password is stored in the DB.
* DB also stores all sensor data.
* The server updates the DB upon user registration or device production.
* The server provides information in the DB to the user.
* The server settings must specify the open server port number, session name and password, DB host, DB username, DB password and DB name.

### Device (Fascio)

* A device runs on a [raspberry pi](https://www.raspberrypi.org/) device whose GPIO #10 is connected to input sensor and GPIO #11 is connected to output.
* A device rejects all inbound attempts at establishing a connection by an [iptables](https://linux.die.net/man/8/iptables) firewall.
* A device attempts to establish a HTTPS connection with the server and authenticates itself to the server with in-device authentication information.
* Device settings (etc/settings.json) contains the authentication information: a unique device ID, device password, and the location(URL) of the server.

### User

* Accesses the server using a browser in HTTPS.
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
	* "frontweb" : "port" is the port the server uses to handle incoming connection requests.
    * "frontweb" : "proxy" is ...
    * "frontweb" : "prefix" is ...
    * "frontweb" : "session" : "name" is the name(id) of the server.
	* "frontweb" : "session" : "secret" is the secret key of the server-user session.
	* "db" : "host" is the MySQL DB host location.
	* "db" : "user" is the MySQL DB username.
	* "db" : "password" is the MySQL DB user password.
	* "db" : "database" is the name of the MySQL DB used by the server for the Toy Model.
1) Register initial devices and default users to the server DB directly (see soviet/sqlscripts/soviet.sql).
1) `npm install`
1) `npm start`

## Fascio Set Up

1) 