# Ansible Playbooks for Home Servers

Designed for systems running `Ubuntu 22.04` and later.


## Setup

* Ensure Ansible is installed, follow: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

* On Mac, Python may be externally managed with Brew. So install with `brew install ansible`

* Check your Ansible version with: `ansible --version`

* Install additional dependencies:
  * Install `sshpass` on Ubuntu 16.04 or similar: `apt-get install sshpass`

  * Install sshpass on Mac OS: `brew install hudochenkov/sshpass/sshpass`


## Pre-requisites

* Ensure all details/file paths etc are correct in `ansible.cfg`

* SSH Key files, some secret variables and template files are encrypted with Ansible vault. Ensure you have the correct password stored in `vault/vault.pass` to decrypt these variables.

* You will need to decrypt the Ansible management user SSH private key, ensure your password to decrypt is specified in `vault/vault.pass` as this will always be used throughout the Playbooks: `ansible-vault decrypt --vault-password-file "vault/vault.pass" "ssh_keys/ansible/id_rsa"`




## Encrypting files with Ansible vault

If your files have not already been encrypted with Ansible vault, run these commands below:

Add your password to the vault password file in `vault/vault.pass`

### Encrypt the SSH Private keys which will be used to communicate with the servers:

* `ansible-vault encrypt --vault-password-file "vault/vault.pass" "ssh_keys/ansible/id_rsa" "ssh_keys/bethany/id_rsa" "ssh_keys/ryan/id_rsa"`

### Encrypt other files which include secret data:

* `ansible-vault encrypt --vault-password-file "vault/vault.pass" "playbooks/web-server/templates/vhost.default.hiccup.config.json"`

### Encrypt individual passwords to a password file vault:

* `ansible-vault encrypt_string --vault-password-file "vault/vault.pass" 'qn4mDx7U7jU7' --name 'management_user_password'`
* `ansible-vault encrypt_string --vault-password-file "vault/vault.pass" 'CmCZ4dNqSTuv' --name 'ryan_password'`
* `ansible-vault encrypt_string --vault-password-file "vault/vault.pass" '7KoZ7GtB68Fb' --name 'bethany_password'`



# Misc Ansible commands

* `ansible-inventory -i inventory.yaml --list`
* `ansible -m ping all -i inventory.yaml`
* `ansible-inventory -i inventory.yaml --graph`



# Ansible playbooks

## General Commissioning
All:
* `ansible-playbook playbooks/commissioning/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Misc
These are intended to be run as seperate Playbooks, so there is no option to run as 'all':
* Ping hosts: `ansible-playbook playbooks/misc/ping.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* List the inventory: `ansible-playbook playbooks/misc/inventory_info.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* Update/Upgrade hosts packages: `ansible-playbook playbooks/misc/update_upgrade_packages_dist.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## File-Server
Individual:
* `ansible-playbook playbooks/file-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* `ansible-playbook playbooks/file-server/configuration.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Start:
* `ansible-playbook playbooks/file-server/start.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Restart:
* `ansible-playbook playbooks/file-server/restart.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Stop:
* `ansible-playbook playbooks/file-server/stop.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/file-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Media-Server
Individual:
* `ansible-playbook playbooks/media-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* `ansible-playbook playbooks/media-server/configuration.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Start:
* `ansible-playbook playbooks/media-server/start.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Restart:
* `ansible-playbook playbooks/media-server/restart.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Stop:
* `ansible-playbook playbooks/media-server/stop.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/media-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## NVR-Server
Install requirements:
* `ansible-galaxy install -r playbooks/nvr-server/meta/requirements.yml`
* `ansible-galaxy collection install -r playbooks/nvr-server/meta/requirements.yml`

Individual:
* `ansible-playbook playbooks/nvr-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/nvr-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Print-Server
Individual:
* `ansible-playbook playbooks/print-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* `ansible-playbook playbooks/print-server/configuration.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Start:
* `ansible-playbook playbooks/print-server/start.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Restart:
* `ansible-playbook playbooks/print-server/restart.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Stop:
* `ansible-playbook playbooks/print-server/stop.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/print-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Web-Server
Individual:
* `ansible-playbook playbooks/web-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
* `ansible-playbook playbooks/web-server/configuration.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Upload custom Web files to the default Vhost web root (E.g. Hiccup - https://github.com/ryanfitton/hiccup-start-page-home-control/):
* `ansible-playbook playbooks/web-server/update_default_vhost_web_root_files.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/web-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

Nginx:
* Start:
  * `ansible-playbook playbooks/web-server/start_nginx.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

* Restart:
  * `ansible-playbook playbooks/web-server/restart_nginx.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

* Stop:
  * `ansible-playbook playbooks/web-server/stop_nginx.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

* Reload:
  * `ansible-playbook playbooks/web-server/reload_nginx.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

PHP:
* Start:
  * `ansible-playbook playbooks/web-server/start_php.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

* Restart:
  * `ansible-playbook playbooks/web-server/restart_php.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

* Stop:
  * `ansible-playbook playbooks/web-server/stop_php.yaml -i inventory.yaml --vault-password-file vault/vault.pass`
  
* Reload:
  * `ansible-playbook playbooks/web-server/reload_php.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Network-Controller-Server
Individual:
* `ansible-playbook playbooks/network-controller-server/commissioning.yaml -i inventory.yaml --vault-password-file vault/vault.pass`

All:
* `ansible-playbook playbooks/network-controller-server/all.yaml -i inventory.yaml --vault-password-file vault/vault.pass`


## Notes

## Commissioning Notes

* Create SSH keys, store these into the folders, each with a user's name: `ssh-keygen -b 4096 -t rsa`

* Move the private and public keys into a folder which matches the username on the system, e.g. `ssh_keys/your_username_here/`


You can these login with these keys, you will need to decrypt the SSH key first:

```
ansible-vault decrypt --vault-password-file "vault/vault.pass" "ssh_keys/ansible/id_rsa"
ssh -i ssh_keys/ansible/id_rsa ansible@10.0.10.106 -p1993
```

```
ansible-vault decrypt --vault-password-file "vault/vault.pass" "ssh_keys/bethany/id_rsa"
ssh -i ssh_keys/bethany/id_rsa bethany@10.0.10.106 -p1993
```

```
ansible-vault decrypt --vault-password-file "vault/vault.pass" "ssh_keys/ryan/id_rsa"
ssh -i ssh_keys/ryan/id_rsa ryan@10.0.10.106 -p1993
```



## File-Server Notes

smb://10.0.10.106/nasdata/

Use same login accounts as system users

Setup configuration for shares in `playbooks/file-server/vars/all.yaml`
By default, this share will be created: `nasdata`
smb://10.0.10.106/nasdata/
You will need to attach the storage disk to this folder: `/datastore/nasdata/`




## Media-Server Notes
Setup URL:
http://10.0.10.105:8096/

Setup configuration for shares in `playbooks/media-server/vars/all.yaml`

You will need to attach the storage disk to this folder: `/datastore/mediadata/`



## NVR-Server Notes
Setup URL: https://10.0.10.107:8971

Setup configuration for shares in `playbooks/media-server/vars/all.yaml`

You will need to attach the storage disk to this folder: `/datastore/cctvdata/`

Logins;
admin
7f87457b50b60528f0e83c1af6734399

See password in docs:
docker logs frigate



## Print-Server Notes
Setup URL: https://10.0.10.108:631
Cups Management user logins:
print
print

Setup configuration for shares in `playbooks/print-server/vars/all.yaml`




## Web-Server Notes
URL: http://10.0.10.109
Only oe vhost setup, default files stored: `/var/www/default`

Setup configuration for shares in `playbooks/web-server/vars/all.yaml`



## Network-Controller-Server Notes
Setup URL:
https://10.0.10.110:8443/

Default username and password:
ubnt
ubnt

Setup configuration for shares in `playbooks/network-controller-server/vars/all.yaml`







todo

cron job service check for:
Frigate - Using Docker
Unifi - Using Docker




