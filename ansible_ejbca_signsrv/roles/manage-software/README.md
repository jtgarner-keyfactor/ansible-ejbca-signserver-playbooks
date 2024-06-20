Manage-Software
==================
Consolidates the acquisition of dependency software required to support an EJBCA deployment.

- Downloads binaries from a remote repository or copies from a local directory
- Installs upstream repository packages
  
Requirements
------------
* Ansible
* Jinja2
 
Role Variables
--------------
### defaults/main.yml
* software_home_dir: /opt

### group_vars/software.yml
#### base variables
* software_home_dir: Base directory for all application installations if a directory is not defined for each software.

#### software dictionaries
* software_wildfly

 
Example Playbook
----------------
```yaml
- hosts: all
  become: true
  roles:
    - configure-software
```
