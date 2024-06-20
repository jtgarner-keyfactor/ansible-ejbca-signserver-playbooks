# Ansible role `fpki_roles_kfeprep`

- Configure JBoss credentials and standalone.xml
- Configure EJBCA properties files
- Deploy EJBCA ear file file
- Create database protection key

**Remarks**
Installing EJBCA should be done through the `fpki_roles_kfsoftware` role.

## Requirements

No specific requirements

## Verify application server exists

It is best to confirm application server exists prior to include JBoss tasks

```yaml
- name: Check if application server exists
  register: found_appsrv_home
  changed_when: false
  ansible.builtin.stat:
    path: "{{ jboss_home }}"
```

## Craete application credential store

```yaml
- name: Check if application server exists
  register: found_appsrv_home
  changed_when: false
  ansible.builtin.stat:
    path: "{{ jboss_home }}"

- name: Create application server credentials
  when: found_appsrv_home.stat.exists
  vars:
    credentials_create_force: true
  ansible.builtin.include_role:
    name: fpki_roles_kfeprep
    tasks_from: create_credentials_store.yml
```

## Configure and Build EJBCA Ear File

Check if EAR file has been successfully deployed before building EAR file

```yaml
- name: Check to see if Ejbca Ear file has been deployed
  register: ejbca_ear_check
  ansible.builtin.stat:
    path: "{{ jboss_home }}/standalone/deployments/ejbca.ear.deployed"

- name: Configure EJBCA and Build EAR file
  when: ejbca_ear_check.stat.exists
  vars:
    credentials_create_force: true
  ansible.builtin.include_role:
    name: fpki_roles_kfeprep
    tasks_from: create_credentials_store.yml    
```

## Build EJBCA tools

Available tools to deploy:
- p11ng-cli
- configdump
- clientToolBox

```yaml
- name: Build p11ng-cli and configdump
  vars:
    ejbca_p11ng_build: true
    ejbca_configdump: true
    ejbca_ctb_build: false
  ansible.builtin.include_role:
    name: fpki_roles_kfeprep
    tasks_from: build_tools.yml    
```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)