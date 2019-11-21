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
* A nginx server receives inbound HTTPS connection, and passes to localhost an HTTP connection to the nodejs server program by [nginx reverse proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/).
* Server rejects all inbound attempts except nginx HTTPS connection (port : 443) and SSH connection (port : 22) by an [iptables](https://linux.die.net/man/8/iptables) firewall.
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
1) Set up nginx HTTPS server and certificate.
    1) Create or get HTTPS certificate.
    ```bash
    # Example: create self-signed certificate
    $ sudo openssl req –new –newkey rsa:2048 –nodes –keyout /etc/ssl/private/server.key –out /etc/ssl/certs/server.csr
    ```
    1) Set up nginx settings file (/etc/nginx/sites-available/default) to use https and reverse proxy.
    ```
    # Example settings file
    server {
        listen 9001 ssl default_server;
        listen [::]:9001 ssl default_server;

        root /var/www/html;

        index index.html index.html;

        server_name _;                                  # your domain name
        ssl_certificate /etc/ssl/certs/server.crt;      # your HTTPS certificate
        ssl_certificate_key /etc/ssl/private/server.key;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers HIGH:!aNULL:!MD5;

        location / {
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header X-Forwarded-Host $host;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header Host $http_host;

                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                chunked_transfer_encoding off;
                proxy_buffering off;
                proxy_cache off;
                proxy_read_timeout 86400;
                keepalive_timeout 86400;

                proxy_pass http://localhost:8080;       # reverse proxy for nodejs program
        }
    }
    ```
    1) Restart nginx.
    ```bash
    $ sudo systemctl restart nginx
    ```
1) To desired directory: `git clone https://github.com/KUSS-Project-19/soviet.git`
1) Modify ./etc/settings.json to desired specifications.
    * "frontweb" : "port" is the port the server uses to handle incoming connection requests.
    * "frontweb" : "proxy" is the 'trust proxy' value of express app. Value is 1 if nginx server is running on localhost - for more information, read [the manual](https://expressjs.com/ko/guide/behind-proxies.html)
    * "frontweb" : "prefix" is empty string "", **do not change**.
    * "frontweb" : "session" : "name" is the session cookie id.
    * "frontweb" : "session" : "secret" is the session secret.
    * "db" : "host" is the MySQL DB host location.
    * "db" : "user" is the MySQL DB username.
    * "db" : "password" is the MySQL DB user password.
    * "db" : "database" is the name of the MySQL DB used by the server for the Toy Model. Default value of sqlscripts/soviet.sql is "soviet".
1) Change default user and device in ./sqlscripts/soviet.sql.
    * Modify the `insert` SQL statements. Make sure to calculate the Bcrypt hashes beforehand.
1) `npm install`
1) `npm start`

## Fascio Set Up

1) Connect electric circuits to Raspberry Pi GPIO properly.
    * GPIO #10 for input sensor, GPIO #11 for output.
    * Alternatively, if you do not have a real physical device, you may use the "debug" simulation mode by setting the environment variable `DEBUG=1`.
1) Modify ./etc/settings.json to desired specifications.
    * "dvid" is the unique device id provided by the IoT company. It is added to the server DB upon production and must exist in the DB to be a valid device.
    * "pass" is the device password.
    * "baseUrl" is the URL of the server (soviet). The HTTPS protocol must be specified (i.e. start with `https://`).
1) `npm install`
1) `npm install onoff` if you are using a real GPIO electric circuit.
1) `npm start`
    * If you are using self-signed certificate, set the environment variable `NODE_TLS_REJECT_UNAUTHORIZED=0` before `npm start`.
