# Ansible-OpenLDAP-Basic
Easy single command deployment for new basic OpenLDAP Nodes

## Quick Start

To run this playbook clone the repository and change the following variables within roles/openldap-compile/vars/main.yml (defaults in brackets:

    sudo_cmd
    admin_usr
  
Update the included hosts file. Then run the playbook:

    ansible-playbook -i hosts deploy_openldap.yml -t "install:all"

## Variables 

    sudo_cmd - Required for privilege escalation [sudo su -]
    admin_usr - User that has permission to start/stop slapd [ansible-adm]
    base_dir - Location of the working directory [/apps]
    src_dir - Location of where the source file will be copied to [/apps/src]
    dependencies - List of dependencies needed to install OpenLDAP
    openldap_version - Version of OpenLDAP to install [2.4.49]
    openldap_install_dir - Location of where OpenLDAP will be installed [/apps/openldap]
    openldap_configs_args - OpenLDAP installation flags 
      [--prefix={{ openldap_install_dir }} --disable-bdb --disable-hdb --enable-ldap --enable-meta --enable-overlays --enable-module]
    working_env - Compilation flags 
      CC - [gcc]
      CFLAGS - [-O -g]
      CPPFLAGS - [-I/appl/openldap/include -I/usr/include -DF00=42]
      LDFLAGS - [-L/appl/openldap/lib -L/usr/lib]
      LIBS - [-lresolv]

## Setup Instructions

1. Clone the repository
2. Modify the variable file (Ansible-OpenLDAP-Basic/roles/openldap-compile/vars/main.yml) as needed. Typically the default entries will work fine. However make note of the following:

  sudo_cmd may be required to be set to install dependencies. Most environments work well using the default [sudo su -], however in the case where you need to use a script for privilege escalation, you will need to modify your script a bit differently. Mike Mikhail over at [link](http://blog.mmikhail.com/2018/09/ansible-how-to-become-different-person.html) has a great article detailing how to modify your privilege escalation scripts to work with ansible.

  You will probably want to change the ansible-adm user to whatever user has permission to launch openldap. The base_dir variable should also be chagned if you do not have an /apps directory. 

  This repository comes with the 2.4.49 OpenLDAP code. If you need to use a more current version you can just swap the tgz file inside roles/openldap-compile/files/ for the newer version and update openldap_version.

  Last, OpenLDAP is compiled using basic flags. If you need additional features compiled inside the slapd executable consider modifying the openldap_configs_args variable.

3. Update the hosts file. Each line corresponds to a target server and the user you are running the playbook with should be able to ssh into the target server (either through a password or ssh key).

For example:

    ldap1 ansible_host=ansibletest ansible_user=ansible-adm

Ensure that the ansible_user entry can ssh into the target server, which in this case is ansibletest. Notice that ldap1 is just a label for the ansibletest server.

4. From the parent directory, run the following command:

To install openldap:

    ansible-playbook -i hosts deploy_openldap.yml -t "install:all"

To start openldap:

    ansible-playbook -i hosts deploy_openldap.yml -t "start:openldap"

To stop openldap:

    ansible-playbook -i hosts deploy_openldap.yml -t "stop:openldap"

## Also See

[Blog Post](https://www.rexconsulting.net/a-beginners-guide-to-deploying-openldap-with-ansible/)
