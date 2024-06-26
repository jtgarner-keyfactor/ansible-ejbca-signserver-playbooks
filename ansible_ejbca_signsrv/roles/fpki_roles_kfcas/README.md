# Ansible role `fpki_roles_kfcas`

- Create Certificate Authorities with the Configdump
- Create Certificate Authorities with the CLI
- Import Configuration Files
- Import CA Certificates
- Verify CA Existence

## Requirements

No specific requirements

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None

## Examples
### Playbook
``` yaml
  - hosts: certificate_authorities[0]
    roles:
      - fpki_roles_kfcas
```

### Import configuration

Define the required variables when including the task:
  - `config_src`: /path/to template files
  - `config_dict`: configdump dictionary containing folder structure and files
  - `import_path`: /path/to configdump root directory to import on remote host


```yaml
- name: Import Initial Certificate Authorities and Configuration
  vars:
    config_src: initial
    config_dict: "{{ configdump_files.initial }}"
    import_path: "{{ kf_ejbca_home }}/initial-config"
  ansible.builtin.include_tasks:
    file: import_config_dump.yml

```

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)