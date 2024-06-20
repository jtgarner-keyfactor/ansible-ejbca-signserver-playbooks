# Ansible role `fpki_roles_kfjboss`

- Configure JBoss service
- Remove unnecessary files
- Modify files and directory permissions

**Remarks**
Installing JBoss should be done through the `fpki_roles_kfsoftware` role.

## Requirements

No specific requirements

## Role Variables

### Basic Configuration

This roles requires a dictionary of values to be declared when include this role in a play.

| Variable                    | Required    | Default                 | Comments                                       |
| :---                        | :---        | :---                    | :---                                           |
| `jboss`                     | Yes         |                         | Dictionary containing application attributes   |
| `jboss_java_opts_xms`       | No          | 2048m                   | Minimun amount of Java heap memory             |
| `jboss_java_opts_xmx`       | No          | 2048m                   | Maximum amount of Java heap memory             |

### JBoss dictionary
An application dictionary consists of the following variables

```yaml
jboss:
  home: /opt/jboss
  user: jboss-eap
  group: jboss-eap                                      
```

## Example playbooks

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

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)