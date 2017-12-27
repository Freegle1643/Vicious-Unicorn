# Vicious-Unicorn
I don't know what this repository is all about.



## Write scripts to automate setup

Sometimes we spend too much time typing the same commands on multiple machines just to setup the same environments and software, which is obviously quite annoying. However, if we organize all those command into a `.sh` file and execute it in another machine we wish to setup, everything is automatized. 

### Set up Docker-ce for Linux

[`install_docker.sh`](https://github.com/Freegle1643/Vicious-Unicorn/blob/master/install_docker.sh)

```shell
#!/bin/bash
sudo apt-get remove docker docker-engine docker.io
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
```

This is the standard Docker-ce installation from [official documentation](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-docker-ce-1). After we create this file we need to make it executable by typing `chmod +x ./install_docker.sh` 

Then we simply run this script by `./install_docker.sh`. Notice that you may want to switch to `root` for this operation. After the process is finished. Switch back to your non-root user and and it to docker group so that we don't need to interact with Docker using `root` privileges.

```bash
sudo usermod -aG docker $USER
```

You may need to logout and log in again to see the result. Type `docker version` and you should see like the following:

```bash
Client:
 Version:      17.09.1-ce
 API version:  1.32
 Go version:   go1.8.3
 Git commit:   19e2cf6
 Built:        Thu Dec  7 22:24:23 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.1-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   19e2cf6
 Built:        Thu Dec  7 22:23:00 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

These steps above reduce the overhead we need to set up Docker especially when set up multiple times on different machines.
