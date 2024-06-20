# Ansible role `fpki_roles_kfconfigdump`

- Only to be used as a wrapper role and included in other tasks
- Subdirectory names are based on the key name values under the `kf_configdump.dirs` dictionary
- Each key must contain a list of file names to template
- Contains all jinja2 templates required for an EJBCA deployment

## Requirements

No specific requirements

## Examples 

### Playbook
```yaml
- name: Create Certificate Authorities and import initial EJBCA configuration
  vars:
    configdump_dict: "{{ kf_configdump.stage_one }}"
  ansible.builtin.include_role:
    name: fpki_roles_kfconfigdump
```

### New configdump import directory

- A add key under the dictionary being passed to `configdump_dict`
- Add the support configdump directories and file names under the `dirs` dictionary

```yaml
# Dictionary
kf_configdump:
  new_dump:
    base: "{{ kf_ejbca_home }}/new_dump" # name of root dir to use in import
    dirs:
      certificate-profiles:
        - cp-gw-Administrator.yaml.j2
        - cp-portal-Administrator.yaml.j2
```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)