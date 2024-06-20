Config-Dump
===========
- Only to be used as a wrapper role and included in other tasks
- Used to check for an existing Configdump directory and create the directory if it does not exist
- Can also be used to remove the existing directory and create a new one.
- All Configdump Template Files are stored in this directory

Requirements
------------
* Ansible
* Jinja2

Namespace
---------
Variables created during this role use the **config_dump** namespace

Role Variables
--------------
### defaults/main.yml

### vars/main.yml

Example Playbook
----------------
1. Add the following task to the play to create the home directory.

    ```yaml
    - name: Create base structure
      include_role:
        name: config-dump
      vars:
        configdump_dir: "{{ example_configdump_dir }}"
        configdump_location: "{{ example_configdump_location }}"
        configdump_reset: "{{ example_configdump_reset }}"
    ```

2. Define the variables being passed in to the config-dump main task. This can be done in roles/default/main.yml of the role including this task or another variables file included in the play. Do not define them with the same variable name to avoid inheritance issues.

    * configdump_dir[string] - Name of Configdump directory 
    * configdump_location[string] - Full path of Configdump directory
    * configdump_reset[boolean] - Indicates if an existing Configdump directory should be removed and recreated

3. Create a list of directories to be created under the home directory. This can be done in roles/default/main.yml of the role including this task or another variables file included in the play.

    ```yaml
    example_subdirectories:
      - certificate-authorities
      - certificate-profiles
      - end-entity-profiles
    ```
