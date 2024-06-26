# Ansible role `fpki_roles_kfpeering`

- Configure global peering settings
- Import External node configurations
- Create peer connectors
- Create peer publishers

## Requirements

- Certificate Authorities need to be created with the `fpki_roles_kfcas` role.
- Apache needs to be configured with the `fpki_roles_kfhttpd` role.

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None.

## Example Playbook
``` yaml
  - hosts: ejbca_servers
    roles:
      - fpki_roles_kfpeering
```

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)