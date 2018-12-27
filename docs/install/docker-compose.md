<h1>Installation</h1>

IsardVDI now works out of the box with docker & docker-compose

[TOC]

# Requirements

. So you only need to have docker service and docker-compose installed:

- Install **Docker**: https://docs.docker.com/engine/installation/

  - Note: docker 17.04 or newer needed for docker-compose.yml v3.2

    - ```bash
      sudo apt install docker
      ```

- Install **docker-compose**: https://docs.docker.com/compose/install/

  - Note: docker-compose 1.12 or newer needed for docker-compose.yml v3.2. You can install it using pip3:

    - ```bash
      sudo apt install python3-pip -y
      sudo pip3 install docker-compose
      ```

# Quickstart

To bring up IsardVDI you only need to download the docker-compose.ym file (or clone the full repo) and bring it up:

```bash
wget https://raw.githubusercontent.com/isard-vdi/isard/master/docker-compose.yml
docker-compose up -d
```

That's all, just connect to **https://<ip|domain>** of the server and follow wizard.

Note: If you do the wizard connecting through *localhost* or *127.0.0.1* the **viewer hostname** in *isard-hypervisor* will be set to that and no one will be able to open viewers.  Refer to :ref:`faq-certificates`

# Insights

## Mapped paths

IsardVDI will create following paths on your system and map it inside hypervisor and app containers:

- **/opt/isard**: The main folder that will contain:
  - **bases**: Path where base template images will be stored. The complete path will include <role>/<category>/<group>/<username>
  - **templates**: Path where user template images will be stored. The complete path will include <role>/<category>/<group>/<username>
  - **groups**: Path where desktop runnable images will be stored. The complete path will include <role>/<category>/<group>/<username>
  - **media**: Path where media (iso and floppy files) will be uploaded. The complete path will include <role>/<category>/<group>/<username>
  - **backups**: Database backups created in web interface using the backup config menu will be stored here.
  - **uploads**: (work in progress)
  - **logs**: Here you will have logs for all the containers. Be aware they could grow so they should be rotated/deleted programatically.
  - **certs**: Certificates for web UI and viewer connections are stored here. Also you can replace initial self-signed certificates with your commercial/letsencrypt ones following the documentation guide about [replacing certificates](certificates.md).

## Build your docker images

If you prefer to build your IsardVDI alpine based docker images you have to clone the full repository (git clone https://github.com/isard-vdi/isard.git) and you will find the docker sources under docker folder:

- **alpine-pandas**: This image has the pandas and numpy python3 libraries. The compilations of those libraries takes long time and, as they are needed for both hypervisor and app containers, we decided to create this image as the base for those images.
- **hypervisor**: It will take alpine-pandas image as base and will bring up a complete KVM/qemu hypervisor with libvirt and a python websockets proxy for browser access to viewers.
- **app**: It will add all the libraries needed to run the engine and webapp IsardVDI source code contained in /src folder of the repo.
- **nginx**: It has the https web server optimized for socketio and websockets. It has also sources for error pages and websocket vnc and spice viewers.
- **rethinkdb**: We use the official rethinkdb image from https://hub.docker.com/_/rethinkdb/.
