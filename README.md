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

Then we simply run this script by `./install_docker.sh`. Notice that you may want to switch to `root` for this operation. After the process is finished. Switch back to your non-root user and add it to docker group so that we don't need to interact with Docker using `root` privileges.

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



## Enable X11SPICE in Docker

[SPICE](https://www.spice-space.org/) provide a much faster and more responsive remote desktop experiences and is widely applied via QEMU. X11VNC is popular in enabling remote graphic access to Docker containers but undertake quality and bandwidth problems when streaming videos or any content with high refresh rate. We want to enable SPICE in container so that we could enjoy graphic desktop experience in Docker, which is quite economical and efficient.

What I use is [x11spice](https://gitlab.com/spice/x11spice), it will enable a running X11 desktop to be available via a Spice server, similar to X11VNC.

### Dockerfile



### An Ugly Way

The reason it's an ugly way is that I currently have to run Docker container with `--privileged` to make sure X display server can be started by container. I'll try to find what are the steps and requirements to make sure container start X without privileged mode in the future. But since my urgent goal is to enable SPICE in Docker container, I shall still try this ugly way. 

#### Start the container

```bash
docker run -it --rm -e DISPLAY=:0 -v /tmp/.X11-unix:/tmp/.X11-unix --device /dev/snd --privileged -p 5961:5900 -p 5971:5901 haoyuan/ubuntu-16.04-x11spice-xfce4-vlc-2:setup
```

#### Start X server

Inside the container, use `xinit` to start X server

#### Start a desktop on the assigned display 

```bash
DISPLAY=:0 startxfce4
```

#### Start X11SPICE in container

```bash
sudo x11spice --display=:0 --allow-control --password=1 172.17.0.2:5900
```

Notice you need to choose the `eth0` IP rather than `lo` IP, which in most of the case is `172.17.0.*`

#### Connect via a spice client

Ubuntu guest is running in my VMware Workstation, and after I start X in container run with `--privileged`, the whole display of Ubuntu guest was occupied and no interaction could be made. I need a spice client to connect, here I use a Windows spice client to connect to it. 

When connecting to the container, we need to use the Ubuntu VM IP and the port projected to `5900` in container. Your VM IP could be known by `ifconfig` inside VM and the port projected to container's `5900` is defined when you run it.

```
spice://192.168.109.132:5961
```

Then type the password in last step, you can interact with your container using SPICE.

