# INSTRUCTIONS

Put here all packages files if you want to use force_local_install = true

Create a folder for these components to put their packages

- jdk: you should have [ins]ONLY ONE[/ins] jdk package in gz format
  - playbook installer will untar any `*.gz` files present.
    - Example: `graalvm-ce-java8-linux-amd64-19.3.6.tar.gz`

  - since 3.10, script executor will rely on GraalVM jdk (jdk1.8 v19.2 or higher)
  - other components still rely on JDK 1.8 (for openJDK taken from https://adoptopenjdk.net/releases.html) or can work with GraalVM
  - so, if you plan to install on a single host, it is recommended to keep only GraalVM jdk

- [OS family] = Debian
  - all Debian family (Ubuntu, ...) DEB packages

- [OS family] = RedHat
  - all Red Hat Family (Rhel, CentOS, Fedora) RPM packages
  - this should be the RPM packages linked to your OS version (except for MySQL)

- [OS family]/: in root folder of the OS family, you should have
  - unzip (required to install vault and SWEAGLE)
  - epel (optional, for Red Hat OS family to install jq)
  - jq (required only to load data in SWEAGLE )
  - policycoreutils-python (for Red Hat OS family to manage SELinux)
  - telnet (optional)
  - screen (optional)
  - playbook installer will search and install any `*.rpm` or `*_amd64.deb` files present in this folder

- [OS family]/elasticsearch
  - playbook installer will look for a file in format elasticsearch-{{ es_version }}.deb or .rpm depending on OS family
  - you should have only the elasticsearch package file
    - Example: `elasticsearch-7.15.2.rpm`

- [OS family]/mongodb: you should have at least these packages
  - /prereqs/* (all packages required for list below)
  - mongodb-org-server
  - mongodb-org-shell
  - policycoreutils-python-2.5 or higher (ex: `policycoreutils-python-2.5-34.el7.x86_64`)
  - python-pymongo

  - playbook installer will look for any `*.deb` or `*.rpm` files depending on OS family
  - it will install first the ones in /prereqs, then root folder

- [OS family]/mysql: you should have for RHEL7 (in this order)
  - `/prereqs/net-tools-2.0-0.20150915git.4.mga6.x86_64`
  - `1-mysql-community-common-5.7.25-1.el7.x86_64`
  - `2-mysql-community-libs-5.7.25-1.el7.x86_64`
  - `mysql-community-client-5.7.25-1.el7.x86_64`
  - `mysql-community-libs-compat-5.7.25-1.el7.x86_64`
  - `mysql-community-server-5.7.25-1.el7.x86_64`
  - `MySQL-python-1.2.5-1.el7.x86_64`

  For RHEL8, as MySQL 8.x is not supported, we keep RHEL7 MySQL 5.7.25 packages, only mySQL-python is upgraded

  - `1-mysql-community-common-5.7.25-1.el7.x86_64`
  - `2-mysql-community-libs-5.7.25-1.el7.x86_64`
  - `mysql-community-client-5.7.25-1.el7.x86_64`
  - `mysql-community-libs-compat-5.7.25-1.el7.x86_64`
  - `mysql-community-server-5.7.25-1.el7.x86_64`
  - `python3-mysql-1.4.6-5.el8.x86_64`

  - playbook installer will look for any `*.deb` or `*.rpm` files depending on OS family
  - it will install first the ones in /prereqs, then root folder

- [OS family]/nginx
  - playbook installer will look for any file like `*nginx*.deb` or `.rpm` depending on OS family.
    - Example: `nginx-1.14.2-1.el7_4.ngx.x86_64.rpm`

- sweagle: you should have
  - `/full-{{YOUR VERSION}}.zip`
  - `/upgrade-{{YOUR VERSION}}.zip` (if you just want to upgrade an existing installation)
  - `/sweagle.key` (to enable SSL, this is your private key file)
  - `/sweagle.pem` (to enable SSL, this is your public certificate key)
  - /sweagleExpert/exporters with cloning of https://github.com/sweagleExpert/exporters.git
  - /sweagleExpert/mditypes with cloning of https://github.com/sweagleExpert/mditypes.git
  - /sweagleExpert/nodetypes with cloning of https://github.com/sweagleExpert/nodetypes.git
  - /sweagleExpert/templates with cloning of https://github.com/sweagleExpert/templates.git
  - /sweagleExpert/validators with cloning of https://github.com/sweagleExpert/validators.git


- vault
  - playbook installer will unzip any file with filename like `*vault_{{ vault_version }}*.zip`
    - Example: `vault_0.7.2_linux_386.zip`


(For 3.13 and upward)
- mysql-jdbc: you should have a java8 MySQL JDBC connector library
  - playbook installer will unzip any file with filename like `mysql*.gz`
    - Example: `mysql-connector-java-8.0.13.tar.gz`

  - downloaded from https://downloads.mysql.com/archives/c-j/


(For 3.21 and upward)
- regex: you should have a java8 Regex library
  - playbook installer will unzip any file with filename like `regex*.gz`
    - Example:`regex-21.1.0.jar.tar.gz`

  - downloaded from https://repo1.maven.org/maven2/org/graalvm/regex/regex/21.1.0/regex-21.1.0.jar


# TROUBLESHOOT

- mysql-jdbc library should be compressed in tar.gz format
If you've got the jar file, you can create this tar.gz file with instruction below

`tar -czvf [target file].tar.gz [source file]`

Example: `tar -czvf mysql-connector-java-8.0.20.jar.tar.gz ./mysql-connector-java-8.0.20.jar`
