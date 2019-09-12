# Docker on raspberry pi

Docker images, stacks and how to for Raspberry Pi

## Install Docker on raspberry pi

The tutorial is for Raspbian or Debian image. For now it will not work on Buster release.
It will not work on Raspberry Pi 1 and Raspberry Zero.

**Install some required packages first**

    sudo apt update
    sudo apt install -y \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg2 \
      software-properties-common

**Get the Docker signing key for packages**

    curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -

**Add the Docker official repos**

    echo "deb [arch=armhf] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
      $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list

**Install Docker**

The aufs package, part of the "recommended" packages, won't install on Buster just yet, because of missing pre-compiled kernel modules. *We can work around that issue by using "--no-install-recommends"*

    sudo apt update
    sudo apt install -y --no-install-recommends \
      docker-ce \
      cgroupfs-mount

**Enable and start docker**

    sudo systemctl enable docker
    sudo systemctl start docker

### Install docker-compose

    sudo apt update
    sudo apt install -y python python-pip libffi-dev python-backports.ssl-match-hostname

    # Install Docker Compose from pip
    sudo pip install docker-compose
