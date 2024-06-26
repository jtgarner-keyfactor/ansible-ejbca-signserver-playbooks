# Ansible role `fpki_roles_kfjboss`

- Configure JBoss service
- Remove unnecessary files
- Modify files and directory permissions

## Requirements

JBoss-EAP needs to be installed with the `fpki_roles_kfsoftware` role.

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

No dependencies.

## Example Playbook
```yaml
# Configure JBoss only when EJBCA ear file hasnt been successfully deployed yet.
- hosts: ejbca_servers
  become: true
  tasks:

    - name: Check to see if Ejbca Ear file has been deployed
      register: ejbca_ear_check
      ansible.builtin.stat:
        path: "{{ jboss_home }}/standalone/deployments/ejbca.ear.deployed"

    - name: Configure JBoss
      when: not ejbca_ear_check.stat.exists
      ansible.builtin.include_role:
        name: fpki_roles_kfjboss
```

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)