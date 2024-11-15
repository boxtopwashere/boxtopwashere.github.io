# Docker Install with Nextcloud

## Overview

Nextcloud is a free, open-source, self-hosted file storage and sharing platform that allows users to manage files in the cloud. Tools and Applications installed on Ubuntu 24.04.1

## Pre Install

Prepare system to install docker without encountering problems.

* follow[docker docs[[[https://docs.docker.com/engine/install/]]]()
*  ensure gnome terminal is installed  
``` sh
sudo apt install gnome-terminal
```

* remove any conflicting packages
```sh
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
                  ```

## Install Docker Desktop

Continuing to follow docker documentation to install Docker Desktop.

* set up Docker's apt repository
``` sh
sudo apt-get update
```

``` sh
sudo apt-get install ca-certificates curl
```

``` sh
sudo install -m 0755 -d /etc/apt/keyring
```

``` sh
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
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
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin
```

* verify installation success
``` sh
sudo docker run hello-world
```

## Nextcloud

I created a secure Nextcloud instance with SSL encryption using Docker Compose.

``` sh
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```sh
sudo chmod +x /usr/local/bin/docker-compose
```

```sh
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

``` sh
sudo usermod -aG docker [user]
```

``` sh
sudo su
su [user]
groups #docker group wont show otherwise
```

``` sh
docker-compose --version
docker --version
```

```
git clone https://github.com/ichiTechs/Dockerized-SSL-NextCloud-with-MariaDB
```

``` sh
docker network ls
docker network create nginx-proxy
```

``` sh
cd Dockerized-SSL-NextCloud-with-MariaDB/
nano docker-compose.yml 
#change VIRTUAL_HOST and LETSENCRYPT_HOST to domain
#change LETSENCRYPT_EMAIL to email
#change MYSQL_ROOT_PASSWORD AND MYSQL_PASSWORD
docker-compose up -d
```