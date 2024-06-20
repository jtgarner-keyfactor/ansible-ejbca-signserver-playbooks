# Ansible role `fpki_roles_kfhttpd`

- Create end entities and TLS certificates for Apache HTTPD
- Configure Apache files
- Enable firewall rules for TLS access

## Requirements

No specific requirements

## Example Playbook
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