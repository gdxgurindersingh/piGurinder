# piGurinder
This is a repo where I document setup of my Raspberry Pi and experiments running on it

# piGurinder
This is a repo where I document setup of my Raspberry Pi and experiments running on it



# Running Grafana, InfluxDB, and Node-RED on a Raspberry Pi with Docker

This guide will walk you through the process of installing Docker, Docker Compose, and running Grafana, InfluxDB, and Node-RED on a Raspberry Pi running Raspberry Pi OS Lite. The services will start automatically with system boot and each service will have a Docker Compose file for easy configuration.

## Prerequisites

- A Raspberry Pi running Raspberry Pi OS Lite
- SSH access to the Raspberry Pi
- A user with sudo privileges, in this guide we will use `gurinder` as the username

## Install Docker

1. Update your system:
```   
sudo apt-get update && sudo apt-get upgrade -y
```

2. Install Docker:
```
curl -sSL https://get.docker.com/ | sh
```

3. Add the `gurinder` user to the `docker` group:
```
sudo usermod -aG docker gurinder
```

## Install Docker Compose

1. Download the latest version of Docker Compose:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. Apply executable permissions to the binary:
```
sudo chmod +x /usr/local/bin/docker-compose
```

3. Verify that the installation was successful:
```
docker-compose --version
```

## Configure Docker Compose to start on system boot

1. Create a systemd unit file for Docker Compose:
```
sudo nano /etc/systemd/system/docker-compose@.service
```


2. Paste the following contents into the `docker-compose@.service` file:
```
[Service]
Restart=always
TimeoutStartSec=0
WorkingDirectory=%i
ExecStart=/usr/local/bin/docker-compose up
ExecStop=/usr/local/bin/docker-compose down

[Install]
WantedBy=multi-user.target
```


3. Save the file and exit.

4. Reload systemd's configuration:
```
sudo systemctl daemon-reload
```

5. Enable your Docker Compose file to start on system boot:
```
sudo systemctl enable docker-compose@docker-compose.service
```

## Create Docker Compose files for each service

1. Create a directory for your Docker Compose files:
```
mkdir ~/docker
```

2. Create a subdirectory for each service:
```
mkdir ~/docker/grafana
mkdir ~/docker/influxdb
mkdir ~/docker/nodered
```

3. In each subdirectory, create a `docker-compose.yml` file with the configuration for that service. For example, here's a sample `docker-compose.yml` file for Grafana:
```
version: '3'

services:
grafana:
image: grafana/grafana:latest
restart: always
ports:
- "3000:3000"
volumes:
- ./data:/var/lib/grafana
- ./logs:/var/log/grafana
```

4. Repeat this step for InfluxDB and Node-RED.

## Organize your services into directories

You can organize your services into directories according to their function or according to which Docker Compose files they use. For example, you could create a `monitoring` directory and put the Grafana, Influx



Create a directory for your Docker Compose files:

bash
Copy code
mkdir ~/docker
Create a subdirectory for each service:

bash
Copy code
mkdir ~/docker/grafana
mkdir ~/docker/influxdb
mkdir ~/docker/nodered
In each subdirectory, create a docker-compose.yml file with the configuration for that service. For example, here's a sample docker-compose.yml file for Grafana:

yaml
Copy code
version: '3'

services:
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - ./data:/var/lib/grafana
      - ./logs:/var/log/grafana

Here's a sample docker-compose.yml file for InfluxDB:
```
version: '3'

services:
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    restart: always
    ports:
      - "8086:8086"
    volumes:
      - ./data:/var/lib/influxdb

```

Here's a sample docker-compose.yml file for Node-RED:
```
version: '3'

services:
  nodered:
    image: nodered/node-red:latest
    container_name: nodered
    restart: always
    ports:
      - "1880:1880"
    volumes:
      - ./data:/data
```

Repeat step 3-5 for InfluxDB and Node-RED.

Once you have created the Docker Compose files for each service, you can start them using the following commands:

```
cd ~/docker/grafana
sudo docker-compose up -d
```
```
cd ~/docker/influxdb
sudo docker-compose up -d
```
```
cd ~/docker/nodered
sudo docker-compose up -d
```

And to stop the services:

```
cd ~/docker/grafana
sudo docker-compose down
```
```
cd ~/docker/influxdb
sudo docker-compose down
```
```
cd ~/docker/nodered
sudo docker-compose down
```
Remember to replace the docker-compose.yml file names in the systemd unit files and sudo systemctl enable commands with the appropriate names for each service.

Here are the steps

For Grafana:
```
sudo sed -i 's/docker-compose.yml/docker-compose-grafana.yml/g' /etc/systemd/system/grafana.service
sudo systemctl enable grafana
```
For InfluxDB:
```
sudo sed -i 's/docker-compose.yml/docker-compose-influxdb.yml/g' /etc/systemd/system/influxdb.service
sudo systemctl enable influxdb
```

For Node-RED:
```
sudo sed -i 's/docker-compose.yml/docker-compose-nodered.yml/g' /etc/systemd/system/nodered.service
sudo systemctl enable nodered
```

These commands will replace the docker-compose.yml file names in the appropriate systemd unit files and enable the services to start automatically at boot.
