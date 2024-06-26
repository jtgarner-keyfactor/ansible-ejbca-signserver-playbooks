# Ansible Playbooks for Deploying EJBCA with JBoss-EAP

These playbooks provide the follow functionalities:

- Installing and configuring EJBCA and the supporting components
- Creating Certificate Authorities and EJBCA configurations
- Configure Apache Httpd to proxy TLS connections
- Deploy External EJBCA Nodes as Peer Connectors and Peer Publishers

## Requirements

- Playbooks that are executed on all hosts may include tasks that only run on certain hosts or host groups.
- A YAML inventory files with host groups defined with the following structure to support inventory conditionals

```yaml
all:
  children:
    certificate_authorities:
    validation_authorities: 
    registration_authorities:
        
    ejbca_servers:
      children:
        certificate_authorities:
        validation_authorities:
        registration_authorities:

    peer_servers:
      children:
        validation_authorities:
        registration_authorities:
```

**Remarks** An INI inventory file may also be used if it support the same structure above.

## Playbook Variables

All playbook variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None.

## Playbooks

### Part1-DeployEjbca.yml
### Executed Tasks
The following tasks are executed:

- Install required software and packages
- Deploy MariaDB
- Configure JBoss
- Configure EJBCA and Build Tools
- Create CA Crypto Tokens and Keys
- Create Certifiate Authorities and Import initial EJBCA configuration
- Create initial Super Administrator certificate
- Configure Apache TLS

### Tags
This play supports the use of the following tags to limit the tasks during execution:

- deploy-ejbca
- create-cas
- config-httpd

### Samples

```bash
# Run all tasks in play
ansible-playbook Part1-DeployEjbca.yml

# Install software and configure EJBCA
ansible-playbook Part1-DeployEjbca.yml --tags deploy-ejbca

# Deploy EJBCA, creates CAs, and import initial configuration
ansible-playbook Part1-DeployEjbca.yml --tags deploy-ejbca, create-cas
```

### Part2-ConfigureEjbcaPeering.yml
### Executed Tasks

- Sync CA Certificates
- Add Super Administrator certficate to Peer Servers
- Create Peering Key Binding
- Configure Peering
- Import remaining EJBCA configurations

### Samples

```bash
# Run all tasks in play
ansible-playbook Part2-ConfigureEjbcaPeering.yml
```

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)