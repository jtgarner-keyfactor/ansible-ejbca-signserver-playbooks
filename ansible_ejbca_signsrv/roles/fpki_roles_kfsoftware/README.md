# Ansible role `fpki_roles_kfsoftware`

- Consolidates the acquisition of dependency software required to support an EJBCA deployment.
- Downloads binaries from a remote repository or copies from a local directory
- Installs upstream repository packages
  
## Requirements

No specific requirements
 
## Role Variables

### Basic configuration

| Variable                       | Default         | Comments                                                                                          |
| :---                           | :---            | :---                                                                                              |
| `app_root_dir`                 | /opt            | Base directory for all application installations if a directory is not defined for each software. |
| `required_packages`            | []              | Packages required by multiple applications used in the EJBCA deployment.                          |
| `software`                     | []              | Dictionary containing application installation variables.                                         |

#### Application dictionary structure

Application must be passed in as a dictionary. Use the following for construction an application dictionary to include in `software` variable when including the role in a play.

```yaml
# Application dictionary. Name of dictionary is not used in task.
# Required: Yes
kf_ejbca_software:

  # Short name reference of application
  # Required: Yes
  name: ejbca

  # Home directory to reference in other tasks if different from path
  # Required: Yes
  home: /opt/ejbca

  # Absolute path of binary after unpacking.
  # Required: Yes
  path: /opt/ejbca_ee_8_2_0_3

  # Application-specific user and group settings
  # Required: No
  # Default: root
  user: jboss-eap
  group: jboss-eap
  create_home: true # create home directory

  # Binary file to unpack
  # Required: Yes
  filename: ejbca_ee_8_2_0_3.zip

  # Parent directory of binary
  # Required: Yes
  source_dir: /opt/media/software-dna/Keyfactor-Ejbca/Software Files/Software Version

  # Create symbolic link to application
  # Required: No
  symlink_enable: true

  # Repository packages required for the installation of this application to be successful
  # Required: No
  dependencies:
    - mariadb
    - mariadb-backup
    - mariadb-common
    - mariadb-server
    - mariadb-server-galera
    - python3-PyMySQL
```
 
## Example Playbook
```yaml
- hosts: ejbca_servers
  become: true
  tasks:

    - name: Install required software and packages
      vars:
        packages: "{{ kf_packages }}"
        applications: "{{ kf_applications }}"
      ansible.builtin.include_role:
        name: fpki_roles_kfsoftware
```

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)