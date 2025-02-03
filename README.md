# Ansible Playbooks for Home Servers

Designed for systems running `Ubuntu 22.04` or `Debian 12.9.0` and later.


## Setup

* Ensure Ansible is installed, follow: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

* On Mac, Python may be externally managed with Brew. So install with `brew install ansible`

* Check your Ansible version with: `ansible --version`

* Install additional dependencies:
  * Install `sshpass` on Ubuntu 16.04 or similar: `apt-get install sshpass`
  * Install `sshpass` on Mac OS: `brew install hudochenkov/sshpass/sshpass`


## Pre-requisites

* Ensure all details/file paths etc are correct in `ansible.cfg`

* SSH Key files, some secret variables and template files are encrypted with Ansible vault. Ensure you have the correct password stored in `vault/vault.pass` to decrypt these variables.

* You will need to decrypt the Ansible management user SSH private key, ensure your password to decrypt is specified in `vault/vault.pass` as this will always be used throughout the Playbooks: `ansible-vault decrypt "ssh_keys/ansible/id_rsa"`




## Encrypting files with Ansible vault

If your files have not already been encrypted with Ansible vault, run these commands below:

Add your password to the vault password file in `vault/vault.pass`

### Encrypt the SSH Private keys which will be used to communicate with the servers:

* `ansible-vault encrypt "ssh_keys/ansible/id_rsa" "ssh_keys/system/id_rsa" "ssh_keys/bethany/id_rsa" "ssh_keys/ryan/id_rsa"`

### Encrypt other files which include secret data:

* `ansible-vault encrypt "playbooks/web-server/templates/vhost.default.hiccup.config.json"`

### Encrypt individual passwords to a password file vault:

* `ansible-vault encrypt_string 'qn4mDx7U7jU7' --name 'management_user_password'`
* `ansible-vault encrypt_string 'kVR91uFaf0' --name 'system_password'`
* `ansible-vault encrypt_string 'CmCZ4dNqSTuv' --name 'ryan_password'`
* `ansible-vault encrypt_string '7KoZ7GtB68Fb' --name 'bethany_password'`



# Misc Ansible commands

* `ansible-inventory -i inventory.yaml --list`
* `ansible -m ping all -i inventory.yaml`
* `ansible-inventory -i inventory.yaml --graph`



# Ansible playbooks

## General Commissioning
This targets: `all` hosts.

All:
* `ansible-playbook playbooks/commissioning/all.yaml -i inventory.yaml`

If you wanted to commision a specific group or node listed in `inventory.yaml` use:
* Node: `ansible-playbook playbooks/commissioning/all.yaml -i inventory.yaml --limit 'proxmox-host01'`
* Group: `ansible-playbook playbooks/commissioning/all.yaml -i inventory.yaml --limit 'WebServers'`


## Misc
These are intended to be run as seperate Playbooks on manually specified hosts, so there is no option to run as 'all':

For exaple, run on the 'WebServers' group by speciying a value for `target`:
* Ping hosts: `ansible-playbook playbooks/misc/ping.yaml -i inventory.yaml --extra-vars "target=WebServers"`
* List the inventory: `ansible-playbook playbooks/misc/inventory_info.yaml -i inventory.yaml --extra-vars "target=WebServers"`
* Update hosts packages: `ansible-playbook playbooks/misc/update_packages.yaml -i inventory.yaml --extra-vars "target=WebServers"`
* Distribution upgrade on hosts packages: `ansible-playbook playbooks/misc/upgrade_distribution.yaml -i inventory.yaml --extra-vars "target=WebServers"`
* Get disk usage: `ansible-playbook playbooks/misc/get_disk_usage.yaml -i inventory.yaml --extra-vars "target=WebServers"`
* Get uptime: `ansible-playbook playbooks/misc/get_uptime.yaml -i inventory.yaml --extra-vars "target=WebServers"`


## File-Server
This targets: `FileServers` hosts.

Individual:
* `ansible-playbook playbooks/file-server/commissioning.yaml -i inventory.yaml`
* `ansible-playbook playbooks/file-server/configuration.yaml -i inventory.yaml`

Start:
* `ansible-playbook playbooks/file-server/start.yaml -i inventory.yaml`

Restart:
* `ansible-playbook playbooks/file-server/restart.yaml -i inventory.yaml`

Stop:
* `ansible-playbook playbooks/file-server/stop.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/file-server/all.yaml -i inventory.yaml`


## Media-Server
This targets: `MediaServers` hosts.

Individual:
* `ansible-playbook playbooks/media-server/commissioning.yaml -i inventory.yaml`
* `ansible-playbook playbooks/media-server/configuration.yaml -i inventory.yaml`

Start:
* `ansible-playbook playbooks/media-server/start.yaml -i inventory.yaml`

Restart:
* `ansible-playbook playbooks/media-server/restart.yaml -i inventory.yaml`

Stop:
* `ansible-playbook playbooks/media-server/stop.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/media-server/all.yaml -i inventory.yaml`


## NVR-Server
This targets: `NvrServers` hosts.

Install requirements:
* `ansible-galaxy install -r playbooks/nvr-server/meta/requirements.yml`
* `ansible-galaxy collection install -r playbooks/nvr-server/meta/requirements.yml`

Individual:
* `ansible-playbook playbooks/nvr-server/commissioning.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/nvr-server/all.yaml -i inventory.yaml`


## Print-Server
This targets: `PrintServers` hosts.

Individual:
* `ansible-playbook playbooks/print-server/commissioning.yaml -i inventory.yaml`
* `ansible-playbook playbooks/print-server/configuration.yaml -i inventory.yaml`

Start:
* `ansible-playbook playbooks/print-server/start.yaml -i inventory.yaml`

Restart:
* `ansible-playbook playbooks/print-server/restart.yaml -i inventory.yaml`

Stop:
* `ansible-playbook playbooks/print-server/stop.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/print-server/all.yaml -i inventory.yaml`


## Web-Server
This targets: `WebServers` hosts.

Individual:
* `ansible-playbook playbooks/web-server/commissioning.yaml -i inventory.yaml`
* `ansible-playbook playbooks/web-server/configuration.yaml -i inventory.yaml`

Upload custom Web files to the default Vhost web root (E.g. Hiccup - https://github.com/ryanfitton/hiccup-start-page-home-control/):
* `ansible-playbook playbooks/web-server/update_default_vhost_web_root_files.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/web-server/all.yaml -i inventory.yaml`

Nginx:
* Start:
  * `ansible-playbook playbooks/web-server/start_nginx.yaml -i inventory.yaml`

* Restart:
  * `ansible-playbook playbooks/web-server/restart_nginx.yaml -i inventory.yaml`

* Stop:
  * `ansible-playbook playbooks/web-server/stop_nginx.yaml -i inventory.yaml`

* Reload:
  * `ansible-playbook playbooks/web-server/reload_nginx.yaml -i inventory.yaml`

PHP:
* Start:
  * `ansible-playbook playbooks/web-server/start_php.yaml -i inventory.yaml`

* Restart:
  * `ansible-playbook playbooks/web-server/restart_php.yaml -i inventory.yaml`

* Stop:
  * `ansible-playbook playbooks/web-server/stop_php.yaml -i inventory.yaml`
  
* Reload:
  * `ansible-playbook playbooks/web-server/reload_php.yaml -i inventory.yaml`


## Network-Controller-Server
This targets: `NetworkControllerServers` hosts.

Individual:
* `ansible-playbook playbooks/network-controller-server/commissioning.yaml -i inventory.yaml`

All:
* `ansible-playbook playbooks/network-controller-server/all.yaml -i inventory.yaml`


## Notes

## Commissioning Notes

* Create SSH keys, store these into the folders, each with a user's name: `ssh-keygen -b 4096 -t rsa`

* Move the private and public keys into a folder which matches the username on the system, e.g. `ssh_keys/your_username_here/`


You can these login with these keys, you will need to decrypt the SSH key first:

```
ansible-vault decrypt "ssh_keys/ansible/id_rsa"
ssh -i ssh_keys/ansible/id_rsa ansible@10.0.10.103 -p1993
```

```
ansible-vault decrypt "ssh_keys/system/id_rsa"
ssh -i ssh_keys/system/id_rsa system@10.0.10.103 -p1993
```

```
ansible-vault decrypt "ssh_keys/bethany/id_rsa"
ssh -i ssh_keys/bethany/id_rsa bethany@10.0.10.103 -p1993
```

```
ansible-vault decrypt "ssh_keys/ryan/id_rsa"
ssh -i ssh_keys/ryan/id_rsa ryan@10.0.10.103 -p1993
```



## File-Server Notes

smb://10.0.10.103/nasdata/

Use same login accounts as system users

Setup configuration for shares in `playbooks/file-server/vars/all.yaml`
By default, this share will be created: `nasdata`
smb://10.0.10.103/nasdata/
You will need to attach the additional storage disk from the hypervisor to this node. `/dev/sdb` mounts in the host to `/datastore/nasdata`.




## Media-Server Notes
Setup URL:
http://10.0.10.102:8096/

Setup configuration for shares in `playbooks/media-server/vars/all.yaml`

You will need to attach the additional storage disk from the hypervisor to this node. `/dev/sdb` mounts in the host to `/datastore/mediadata`.



## NVR-Server Notes
Setup URL: https://10.0.10.104:8971

Setup configuration for shares in `playbooks/media-server/vars/all.yaml`

You will need to attach the additional storage disk from the hypervisor to this node. `/dev/sdb` mounts in the host to `/datastore/nvrdata`.

Logins;
admin
7f87457b50b60528f0e83c1af6734399

See password in docs:
docker logs frigate



## Print-Server Notes
Setup URL: https://10.0.10.101:631
Cups Management user logins:
print
print

Setup configuration for shares in `playbooks/print-server/vars/all.yaml`




## Web-Server Notes
URL: http://10.0.10.105
Only oe vhost setup, default files stored: `/var/www/default`

Setup configuration for shares in `playbooks/web-server/vars/all.yaml`



## Network-Controller-Server Notes
Setup URL:
https://10.0.10.106:8443/

Default username and password:
ubnt
ubnt

Setup configuration for shares in `playbooks/network-controller-server/vars/all.yaml`







todo

cron job service check for:
Frigate - Using Docker
Unifi - Using Docker





Mount hard drive in vm:


fileserver mount hard drive at folder:

/datastore/nasdata/

df -h

lsblk


sdb

https://forum.proxmox.com/threads/can-i-create-a-zfs-volume-in-proxmox-then-mount-and-format-it-on-my-vm.71443/


specify disk path: /dev/sdb
check the path is formatted
mount the datapath to the folder: /datastore/nasdata

format the disk




Then, increase the attached disk size to that in the spreadsheet