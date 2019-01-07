<h1>Hypervisors</h1>

[TOC]

# Types of hypervisors

## Pure hypervisor

When you edit or create an hypervisor you can check if it will be only an hypervisor. In this mode that server will only be used by IsardVDI to run virtual desktops. No virtual disks operations will be handled by this server.

## Pure disk operations

In this mode the server will only be used to handle virtual desktops disk operations.

## Mixed hypervisor + disk operations

This mode will use the server for both running virtual desktops and executing virtual disk operations.

# External hypervisors

If you want to use another host as the KVM hypervisor ensure it has:

- **libvirtd service running**: ```systemctl status libvirtd```. Check with your distro how to install a complete KVM hypervisor:
  - https://wiki.debian.org/KVM#Installation
  - https://help.ubuntu.com/community/KVM/Installation
  - https://wiki.centos.org/HowTos/KVM
  - https://wiki.alpinelinux.org/wiki/KVM
- **sshd service running and reacheable for user**: ```systemctl status sshd```
- **curl**: It will be used to get updates. 
- **qemu-kvm**: You should create link to your qemu-system-x86_64 like this ```ln -s /usr/bin/qemu-system-x86_64 /usr/bin/qemu-kvm```

You should previously add ssh keys to access that hypervisor. We do provide an script inside isard-app container to do this:

```bash
docker exec -e HYPERVISOR=<IP|DNS> -e PASSWORD=<YOUR_ROOT_PASSWD> isard_isard-app_1 bash -c '/add-hypervisor.sh'
```

After doing this you should go to hypervisors menu and add a new hypervisor with the same IP/DNS. Engine will try to connect when you enable that hypervisor.

**Example:**

Note that whe added ``; history -d $((HISTCMD-1))`` to the command to avoid password being kept in our history.

```bash
root@server:~/isard# docker exec -e HYPERVISOR=192.168.0.114 -e PASSWORD=supersecret isard_isard-app_1 bash -c '/add-hypervisor.sh'; history -d $((HISTCMD-1))
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/edge/testing/x86_64/APKINDEX.tar.gz
(1/1) Installing sshpass (1.06-r0)
Executing busybox-1.28.4-r2.trigger
OK: 235 MiB in 127 packages
Trying to ssh into 192.168.0.114...
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
expr: warning: '^ERROR: ': using '^' as the first character
of a basic regular expression is not portable; it is ignored

/usr/bin/ssh-copy-id: WARNING: All keys were skipped because they already exist on the remote system.
		(if you think this is a mistake, you may want to use -f option)

Hypervisor ssh access granted.
Access to 192.168.0.114 granted and found libvirtd service running.
Now you can create this hypervisor in IsardVDI web interface.
```

If it fails, disable hypervisor, edit parameters and enable it again. IsardVDI engine will check connection again. You can see failed message in hypervisor details (click on + button to see details).

If engine is not trying to connect again after enabling new hypervisor follow to restart isard-app container:

```bash
docker restart isard_isard-app_1
```

## Infrastructure concepts

When you add an external hypervisor you should be aware (and configure as needed) of:

- **Disks path**: By default it will store disks in /isard. That folder doesn't need to be created but it is recommended that speedy IO storage (like nvme disk) is mounted in that path.
- **Network performance**: If you are going to use a NAS you should take into account that speedy network must be used. Recommended network speed over 10Gbps. 

### Disks performance

As different paths are used for bases, templates and groups disks you could improve performance mounting different storage nvme disks in each path:

- **/isard/bases**: There will reside the base images you create. Usually those disk images will be used as base for many templates and user disks (groups) so the storage should provide as quick read access as possible.
- **/isard/templates**: Templates will also be used as main disks for multiple user disks (groups). So better use a quick read access disk.
- **/isard/groups**: Running desktops will reside here. So a lot of concurrent IOs will be done here so the storage should provide as much IOPS as possible. NVME disks (or even NVME raids) will bring the best performance.

### Network performance

The storage network between hypervisors and NAS storage servers should be at least 10Gbps. All hypervisors should mount storage in **/isard** path and that mount should be the same for all hypervisors.

As you can choose when creating hypervisor to be a **pure hypervisor**, **pure disk operations** or a **mixed one** you could add NAS storage servers as pure disk operations. That will bring a quicker disks manipulation as IsardVDI will make use of direct storage and commands on NAS.

We have an IsardVDI infrastructure with six hypervisors and two pacemaker clustered NAS self made that share storage with NFSv4 and the performance is very good. We have our technical documents at thedocs.isardvi.com