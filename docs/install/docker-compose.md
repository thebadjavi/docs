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
docker-compose pull
docker-compose up -d
```

That's all, just connect to **https://<ip|domain>** of the server and follow wizard.

Note: If you do the wizard connecting through *localhost* or *127.0.0.1* the **viewer hostname** in *isard-hypervisor* will be set to that and no one will be able to open viewers.  Refer to  [troubleshoot incorrect viewer hostname](../admin/faq.md#tries-to-connect-to-localhost-or-incorrect-iphostname).

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
