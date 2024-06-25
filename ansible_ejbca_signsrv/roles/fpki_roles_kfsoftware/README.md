# Ansible role `fpki_roles_kfsoftware`

- Consolidates the acquisition of dependency software required to support an EJBCA deployment.
- Downloads binaries from a remote repository or copies from a local directory
- Installs upstream repository packages
  
## Requirements

Software binaries already transfer locally or available through the upstream repository

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

  - hosts: ejbca_servers
    roles:
        - fpki_roles_kfsoftware

## License

TBD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)