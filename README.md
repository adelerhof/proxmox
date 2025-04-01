<div style="border: 2px solid #d1d5db; padding: 20px; border-radius: 8px; background-color: #f9fafb;"> <h2 align="center">Proxmox VE Helper-Scripts: A Community Legacy</h2> <p>Dear Community,</p> <p>As we prepare to archive this repository, we’re excited to announce a new home for the project: <a href="https://github.com/community-scripts/ProxmoxVE">https://github.com/community-scripts/ProxmoxVE</a>. While we’re still settling in, this will soon become the go-to place for the latest Proxmox VE scripts.</p> <p>Thank you for your continued support.</p> <br>The Community </div>
<div align="center">
  <a href="#">
    <img src="https://raw.githubusercontent.com/tteck/Proxmox/main/misc/images/logo.png" height="100px" />
 </a>
</div>
<h1 align="center">Proxmox VE Helper-Scripts</h1>

<p align="center">
  <a href="https://helper-scripts.com">Website</a> | 
  <a href="https://github.com/tteck/Proxmox/blob/main/.github/CONTRIBUTING.md">Contribute</a> |
  <a href="https://github.com/tteck/Proxmox/blob/main/USER_SUBMITTED_GUIDES.md">Guides</a> |
  <a href="https://github.com/tteck/Proxmox/blob/main/CHANGELOG.md">Changelog</a> |
  <a href="https://ko-fi.com/D1D7EP4GF">Support</a>
</p>

---

These scripts empower users to create a Linux container or virtual machine interactively, providing choices for both simple and advanced configurations. The basic setup adheres to default settings, while the advanced setup gives users the ability to customize these defaults. 

Options are displayed to users in a dialog box format. Once the user makes their selections, the script collects and validates their input to generate the final configuration for the container or virtual machine.
<p align="center">
Be cautious and thoroughly evaluate scripts and automation tasks obtained from external sources. <a href="https://github.com/tteck/Proxmox/blob/main/CODE-AUDIT.md">Read more</a>
</p>
<sub><div align="center"> Proxmox® is a registered trademark of Proxmox Server Solutions GmbH. </div></sub>


Copied from original post: [tteck/Proxmox#2072](https://github.com/tteck/Proxmox/discussions/2072)
## Setting up Cloud-Init
![image](https://github.com/adelerhof/proxmox/blob/main/img/proxmox-cloud-init.png)

    1. Set user to root

    2. Give root user a password

    3. Set to no (not enough storage, yet)

    4. Set network settings

    5. Regenerate Image


**Now you can start the VM**
### Select xterm.js under the Console pull down for copy/paste functions

![image](https://github.com/adelerhof/proxmox/blob/main/img/proxmox-xterm.png)
## Resize the Bootdisk (/dev/sda)
### Hardware > Hard Disk (scsi0) > Disk Action > Resize

![image](https://github.com/adelerhof/proxmox/blob/main/img/proxmox-disk-resize.png)
## Expand VM Disk using parted (/dev/sda1)

`parted /dev/sda` `resizepart 1` Fix/Ignore? `Fix` Partition number? `1` Yes/No? `Yes` End? [2146MB]? `-0` (parted) `quit` **reboot**
## Get SSH Going

```
sed -i -e 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' -e 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
rm /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
systemctl restart sshd
```

## Add Guest Agent

```
apt-get update && apt-get -y upgrade
apt-get install -y qemu-guest-agent
```

**reboot**
## Install Docker

```
sh <(curl -sSL https://get.docker.com)
```

## Install Docker Compose

```
LATEST=$(curl -sL https://api.github.com/repos/docker/compose/releases/latest | grep '"tag_name":' | cut -d'"' -f4)
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -sSL https://github.com/docker/compose/releases/download/$LATEST/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker compose version
```

