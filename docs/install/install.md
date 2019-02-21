<h1>Installation</h1>

IsardVDI now works out of the box with docker & docker-compose

[TOC]

# Requirements

You only need to have docker service and docker-compose installed:

- Install **Docker**: https://docs.docker.com/engine/installation/

  - Note: docker 17.04 or newer needed for docker-compose.yml v3.2

    - ```bash
      sudo apt install docker
      ```

- Install **docker-compose**: https://docs.docker.com/compose/install/

  - Note: docker-compose 1.12 or newer needed for docker-compose.yml v3.5. You can install last version using pip3:

    - ```bash
      sudo apt install python3-pip -y
      sudo pip3 install docker-compose
      ```

**NOTE**: Your hardware needs to have virtualization enabled. You can check that in your BIOS but also from CLI:

```
egrep ‘(vmx|svm)’ /proc/cpuinfo
```
​	If you see nothing in the output your CPU has no virtualization capabilites or they are disabled in BIOS.

# Quickstart

To bring up IsardVDI you only need to download the docker-compose.yml file (or clone the full repo if you want to build the images yourself) and bring it up:

```bash
wget https://raw.githubusercontent.com/isard-vdi/isard/master/docker-compose.yml
docker-compose pull
docker-compose up -d
```

That's all, just connect to **https://<ip|domain>** of the server and follow [wizard](wizard.md).

Note: If you connect with your browser to the wizard through *localhost* or *127.0.0.1* the **viewer hostname** in *isard-hypervisor* will be set to that IP and no one will be able to open viewers from other computers.  Refer to  [troubleshoot incorrect viewer hostname](../admin/faq.md#tries-to-connect-to-localhost-or-incorrect-iphostname) to modify the viewer IP.

# Insights

## Mapped paths

IsardVDI will create following paths on your system and map it inside hypervisor and app containers:

- **/opt/isard**: The main folder that will contain:
  - **bases**: Path where base template images will be stored. The complete path will include `/opt/isard/bases/<role>/<category>/<group>/<username>/<base_disk_name.qcow2>`
  - **templates**: Path where user template images will be stored. The complete path will include `/opt/isard/templates/<role>/<category>/<group>/<username>/<tmpl_disk_name.qcow2>`
  - **groups**: Path where desktop runnable images will be stored. The complete path will include `/opt/isard/group/<role>/<category>/<group>/<username>/<desktop_disk_name.qcow2>`
  - **media**: Path where media (iso and floppy files) will be uploaded. The complete path will include `/opt/isard/media/<role>/<category>/<group>/<username>/<media_(iso|floppy).(iso|fd)>`
  - **backups**: Database backups created in web interface using the backup config menu will be stored here.
  - **uploads**: (work in progress)
  - **logs**: Here you will have logs for all the containers. Be aware they could grow so they should be rotated/deleted programatically.
  - **certs**: Certificates for web UI and viewer connections are stored here. Also you can replace initial self-signed certificates with your commercial/letsencrypt ones following the documentation guide about [replacing certificates](certificates.md). In the actual version IsardVDI website and viewers make use of the same certificates stored at `/opt/isard/certs/default/` path location.

## Build your docker images

If you prefer to build your IsardVDI alpine based docker images you have to clone the full repository (git clone https://github.com/isard-vdi/isard.git) and you will find the docker sources under docker folder:

- **alpine-pandas**: This image has the pandas and numpy python3 libraries. The compilations of those libraries takes long time and, as they are needed for both hypervisor and app containers, we decided to create this image as the base for those images.
- **hypervisor**: It will take alpine-pandas image as base and will bring up a complete KVM/qemu hypervisor with libvirt and a python websockets proxy for browser access to viewers.
- **app**: It will add all the libraries needed to run the engine and webapp IsardVDI source code contained in /src folder of the repo.
- **nginx**: It has the https web server optimized for socketio and websockets. It has also sources for error pages and websocket vnc and spice viewers.
- **rethinkdb**: We use the official rethinkdb image from https://hub.docker.com/_/rethinkdb/.

We do provide a build script for dockers. You only need to add version parameter:

```bash
./build-docker-images.sh 1.0.1
```

After building images from source you can start it with ```docker-compose up -d```.

NOTE: Check the version of containers in docker-compose.yml file to build the same version.

# Sample installs

## Debian 9 Stretch

With a fresh debian 9 install you can install docker and docker-compose with this commands.

### Install docker

```bash
apt-get remove docker docker-engine docker.io containerd runc
apt-get install     apt-transport-https     ca-certificates     curl     gnupg2     software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
apt-get update
apt-get install docker-ce
```

### Install docker-compose
```bash
apt install python3-pip
pip3 install docker-compose
```
## Fedora 28-29

With a fresh Fedora 28-29 install you can install docker and docker-compose with this commands.

### Install docker

```bash
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine

sudo dnf -y install dnf-plugins-core
sudo dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### Install docker-compose
```bash
yum install python3-pip
pip3 install docker-compose
```

# Troubleshooting Install

## docker-compose up -d refuses to start hypervisor

The may be two possible sources for this problem. One is the use of a service in your host that is on a port in the range of default ports used by hypervisor and viewers. Those ports are:

- 5900-5949: Spice viewer port range
- 55900-55949: web browser viewer ports.

You can check your listening ports by issuing the command **netstat -tulpn** and checking if any of your listening ports overlaps with hypervisor port range. 

There is no easy solution to this without shutting down your service before starting IsardVDI. It happens that *chromium* in Linux starts listening for *mDNS* service on port 5353. This can be easily solved by using another browser like *firefox* to access IsardVDI or starting IsardVDI containers before opening *chromium browser*. Note that this will happen only if your are going to use the same computer to run IsardVDI and access it through a local browser in the server.

## After doing the wizard the hypervisor details say there is no virtualization available

Some CPUs (mostly old ones) don't have hardware virtualization, others have it but it is disabled in BIOS. In the first case there is nothing that can be done. If it is disabled in BIOS then you should check for VT-X or Virtualization or SVM and activate it.

[More info in admin faqs](../admin/faq.md#after-finishing-install-the-default-isard-hypervisor-is-disabled)

# Nested installation in KVM

Check for nested virtualization option in your host operating system:

- Intel processors: ```cat /sys/module/kvm_intel/parameters/nested```
- AMD processors: ```cat /sys/module/kvm_amd/parameters/nested```

It should show a **1** or **Y** if it is enabled.

You will need to enable nested virtualization on your host operating system if not active yet.

## Nested virt in Intel processors:

### Live

With all VMs stopped remove kvm_intel module
```
modprobe -r kvm_intel
```
And load it again with nested option:
```
modprobe kvm_intel nested=1
```

### Permanent

Create the file ```/etc/modprobe.d/kvm.conf``` and add inside:
```
options kvm_intel nested=1
```

## Nested virt in AMD processors:

### Live

With all VMs stopped remove kvm_amd module
```
modprobe -r kvm_amd
```
And load it again with nested option:
```
modprobe kvm_amd nested=1
```

### Permanent

Create the file ```/etc/modprobe.d/kvm.conf``` and add inside:
```
options kvm_amd nested=1
```

# Installing IsardVDI inside VMWare ESXi guest

Enable host CPU passthrough to the guest. That should be enough.
