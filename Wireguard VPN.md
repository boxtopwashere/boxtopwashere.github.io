# Wireguard VPN on Digital Ocean

## Overview

This guide will set up a Wireguard VPN server on a Ubuntu 24.10 Docker Droplet. It will create the droplet on digital ocean
## Digital Ocean

Digital Ocean is a cloud computing platform that provides infrastructure and tools for developers, startups, and small and medium-sized businesses. 

New Accounts in Digital Ocean get $200 credit for 60 days which I utilized for this server.  Visit to gain access.

After you have logged in, create a Droplet within a project . Choose whatever plan you want as long as it is Ubuntu, you have at least 4gb or RAM and 1 vCPU for a competent server.

## Docker Install

Install Docker on Droplet

* remove conflicting packages
``` sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

* set up Docker's apt repository
``` sh
sudo apt-get update
```

``` sh
sudo apt-get install ca-certificates curl
```

``` sh
sudo install -m 0755 -d /etc/apt/keyrings
```

``` sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

``` sh
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

``` sh
sudo apt-get update
```

* install Docker Packages
``` sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

* verify installation success
``` sh
sudo docker run hello-world
```

## Docker Compose
create Docker compose file for VPN setup with multiple peer connections

``` sh
mkdir wireguard && cd wireguard
```

* make docker-compose.yml file with the following content
```
version: '2.1'
services:
  wireguard:
    image: lscr.io/linuxserver/wireguard:latest
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE 
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/North_Dakota/Center
      - SERVERURL= <ip>
      - SERVERPORT=51820 
      - PEERS=desktop,phone 
      - PEERDNS=auto 
      - INTERNAL_SUBNET=10.0.0.0 l
    volumes:
      - ./config:/config 
      - /lib/modules:/lib/modules
    ports:
      - 51820  
      - 
      - :51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: always
```

``` sh
sudo docker compose up -d
```

## VPN Connect
VPN Connect logs and connections

``` sh
sudo docker compose logs -f wireguard
```

* use revealed qr code for phone but must obtain config file for desktop
``` sh
sudo scp root@<ip>:/root/wireguard/config/peer_<peer_name>/peer_<peer_name>.conf . 
# Replace <ip> with your server's IP address and <peer_name> with the name specified in your docker compose file.
```

## VPN Demonstration

* phone 

![[phone_wireguard.png]]

* desktop

![[desktop_wireguard.png]]