# Ansible role `fpki_roles_kfcas`

- Create Certificate Authorities with the Configdump
- Create Certificate Authorities with the CLI
- Dynamically create YAML config files
- Verify CA Existence
- Import External Certificiate Authorities
- Update Existing OCSP Presigner Service

## Requirements

No specific requirements

Example Playbook
----------------
``` yaml
  - hosts: certificate_authorities[0]
    roles:
      - fpki_roles_kfcas
```

## Create certificate authority with configdump

Define the 'import_path' of the configdump structure to import when including the task

```yaml
- name: Create Certificate Authorities with Configudmp
  vars:
    import_path: "{{ kf_ejbca_home }}/initial-config"
  ansible.builtin.include_tasks:
    file: create_ca_dump.yml
```

## Dynamically create template files

- Define dictionary containing individual dictionaries for each config
- The key name of each dictionary will be used as the `name` value in each templated yaml
- It is required to define the following vars when including the tasks:
  - `config_dict`: variables dictionary containing values to template
- Include assertion task to validate required vars are defined

### Create ACME aliases

```yaml
# Define alias dictionary in the `kf_acme_aliases` variable
kf_acme_aliases:
  src: alias-config.yaml.j2
  path: "{{ kf_ejbca_home }}/initial-config/acme-config"
  files:
    rsa:
      eeprofile: PublicTrust90DayTLSRSA
      challenge_types: http-01,tls-alpn-01
      dns_resolver: 192.168.114.10
      order_validity: 3600000

# Include task and define required variables
- name: Template ACME alias configdump files when dictionary has items
  when: kf_acme_aliases | default({}) | length
  vars:
    config_dict: "{{ kf_acme_aliases }}"
  include_tasks: dynamic_config_dump.yml
```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)