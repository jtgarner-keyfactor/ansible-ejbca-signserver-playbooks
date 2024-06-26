# Ansible role `fpki_roles_kfhttpd`

- Create end entities and TLS certificates for Apache HTTPD
- Configure Apache files
- Enable firewall rules for TLS access

## Requirements

Apache HTTPD needs to be installed with the `fpki_roles_kfsoftware` role.

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None

## Examples

### Create end entites

- List of entities is dynamically created using the `inventory_hostname` of each host included in the play execution
- `reset_end_entity` can be included as `true` when including `create_tls_certs.yml` to revoke an existing end entity and set the status to NEW.

```yaml
- name: Create, or reissue ACTIVE, TLS certificates for each host in 'ansible_host_fqdns' on {{ groups['certificate_authorities'][0] }}
  when: inventory_hostname in groups['certificate_authorities'][0]
  loop: "{{ ansible_host_fqdns }}"
  run_once: true
  vars:
    end_entity_reset: true
  ansible.builtin.include_tasks: 
    file: create_tls_certs.yml
```

### Playbook
``` yaml
  - hosts: ejbca_servers
    roles:
      - fpki_roles_kfhttpd
```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)