# Ansible role `fpki_roles_kfcas`

- Create Certificate Authorities with the CLI
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

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)