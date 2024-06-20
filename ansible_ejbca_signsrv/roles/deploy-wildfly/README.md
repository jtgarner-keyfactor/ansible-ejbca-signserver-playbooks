Deploy-Wildfly
==============
Adds hostname and IPv4 address to remote host /etc/hosts file
  
Requirements
------------
* Ansible
* Jinja2
 
Role Variables
--------------

### defaults/main.yml
Default hostname variables.

* hostname: Fully qualified hostname of remote server
* ipaddress: IPv4 network address of remote server
 
Example Playbook
----------------
```yaml
- hosts: all
  become: true
  roles:
    - deploy-wildfly
```
