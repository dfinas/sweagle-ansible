# sweagle-ansible

This is Ansible playbook to install SWEAGLE on-premise.
It supports :
-	Installation only SWEAGLE or SWEAGLE with prerequisites
-	Independent installation of each component or full install
-	Idem potent, in case of error, correct, then restart script
-	Initialisation of Vault with automatic capture of init token in SWEAGLE config and automatic unseal
-	Full silent install or interactive mode (for example, if you don’t put a license key, it will ask for it)
- All in one server installation or multi-servers splitted by component
- Installation from local package (when no access to Internet from hosts)
  - with full scope for Red Hat family, and limited scope for Debian
- SSL configuration for Nginx

## Prerequisites:
- You should run this playbook as root or sudoer user or put sudoer user/password in your inventory file
- SELinux must be disabled (check with command `sestatus`, you can run system role to disable it, then reboot your server)
- Ansible 2.4 or higher + package sshpass
- be sure ansible.cfg knows your inventory folder and authorize non ssh checks:
more /etc/ansible/ansible.cfg

## WARNING
- Copy your SWEAGLE full package zip file to `/files/sweagle` when force_local_installation is set to true

- For SSL, copy your certificate and private key files in  /files/sweagle
  - to generate a self-signed certificate, use:
`sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./files/sweagle/sweagle.key -out ./files/sweagle/sweagle.pem`

- when local installation, put all components and prerequisites files in /files
(see /files/README.md for details)

- Update your inventories files with your root password for Ansible become to work
  - this is in /inventories/all-in-one/all.yml for full installation on one host
  - this is in /inventories/multi-hosts/hosts.yml for installation on multiple hosts

- Update playbook properties with your desired settings (for custom installation path, ports to use, etc) in
  - /group_vars/all.yml
  - you can put here your SWEAGLE license in parameter sweagle_license_key for silent install
(if you don't put your license, then playbook will ask for it when installing SWEAGLE component)
- if you set force_local_installation to true, be sure to put all required packages files in /files folder (see ./files/README.md for details)

## Test with

`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml --check`

(be aware that checks will fail on tasks requiring update to occur)

## For full installation (including prerequisites)
`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml`

- If localhost installation
`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml --connection local`

## For SWEAGLE installation (prerequisites already installed)
- check before that prerequisites are well installed, then:
`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml --tags sweagle`

## To install only a specific component or prerequisite
`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml --tags <COMPONENT>`

Components available are:
- elasticsearch
- jdk
- mongodb
- mysql
- nginx
- sweagle (all prerequisites components should be present)
- sweagle-data (load initial data on existing tenant)
- sweagle-web (only install the webserver tier - Nginx must be there !)
- system (disable SELinux and install prerequisites libs like unzip, or jq)
- vault

Tags must be put in lowercase, example to install only MySQL:
`ansible-playbook all-install.yml -i ./inventories/all-in-one/hosts.yml --tags mysql`


## TESTED ON
In the list below, only latest combinations of OS and Sweagle/components versions are tested together

ANSIBLE:
- Ansible 2.4.2, 2.5.1, 2.8.5, 2.9.6, 2.9.10, 2.9.27

OS:
- Ubuntu 18.04
- CentOS 7.6, 7.7, 7.8, 8.5

SWEAGLE:
- Sweagle 3.8, 3.9, 3.10, 3.11, 3.12, 3.13, 3.22
  - for 3.11 and higher, be sure to use GraalVM JDK for ScriptExecutor
  - for 3.13 and higher, be sure to add mysql-connector-java-8.0.xx.tar.gz in files folder (tested with 8.0.13 and 8.0.20)

PREREQUISITES:
- JDK: OpenJDK 8u242 and starting SWEAGLE 3.13 and higher: GraalVM 19.3.1, 21.1.0
- ElasticSearch 6.6.2 (for SWEAGLE below 3.10), 6.8.6 (for SWEAGLE 3.10 to 3.13), 6.8.12 (for SWEAGLE 3.13 to 3.22), 7.15.2 (for SWEAGLE 3.22 and higher)
- Mongo 3.6 (for SWEAGLE to 3.13), 4.0.20 (for SWEAGLE 3.13 to 3.22), 4.4.10 (for SWEAGLE 3.22 and higher)
- MySQL 5.7.25
- MySQL JDBC connector 8.0.13, 8.0.27
- Nginx 1.14.2 and 1.20.2
- Regex Java library 21.1.0
- Vault 0.7.2 and 1.0.3


## TROUBLESHOOT
- Vault 1.1.x and upper are not supported and doesn't work with SWEAGLE (as of SWEAGLE 3.1.x)
- MySQL 8.0.x is not supported and doesn't work with SWEAGLE (as of SWEAGLE 3.22)
- Force_local_installation may not work depending on dependencies present or not on target host
- Ubuntu 18.04 repository contains only Mongo 3.6, so MongoDB 4.0 can be installed only with local package for this release


## Todo list

- Check versions for prerequisites before installation
- Ex: on MongoDB, when another release is installed, remove it before
  - add a new parameter mongodb_remove_other_releases: false (by default)
  - add tasks to remove other release
  - currently, plays failed and you should remove manually
  - reproduced on centos 7.x where default mongo is 2.6

- For Vault, read keys* file to add auto-unseal even if install fails after vault init
