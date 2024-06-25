# Ansible role `fpki_roles_kfmariadb`

- Remove unsafe defaults:
    - set database root password (remark that once set, this role is unable to *change* the database root password)
    - remove anonymous users
    - remove test database
- Create users and databases
- Uses variables nested in a dictionary to allow for the creation of multiple databases without using a list of dictionaries.

## Requirements

MariaDB needs to be installed with the `fpki_roles_kfsoftware` role.

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None

## Examples

### Single Database and User
```yaml
mariadb_databases:
  ejbca: 
    name: ejbca
    user: ejbca-usr
    password: "{{ kf_ejbca_vault_db_pass|default('ejbca') }}"
    priv: "ejbca.*:ALL"        
    sql_scripts:                      
      - create-tables-ejbca-mysql.sql
      - create-index-ejbca.sql
```

### Multiple databases and Users
```yaml
mariadb_databases:

  # Database protection and sql scripts
  ejbca-ca: 
    name: ejbca
    user: ejbca-usr
    password: "{{ kf_ejbca_vault_db_pass|default('ejbca') }}"
    priv: "ejbca.*:ALL"        
    sql_scripts:                      
      - create-tables-ejbca-mysql.sql
      - create-index-ejbca.sql

  # No database protection or SQL Scripts
  ejbca-peers: 
    name: ejbca-peers
    user: ejbca-peers-usr
    password: "{{ kf_ejbca_vault_db_pass|default('ejbca') }}"
    priv: "ejbca.*:ALL"        
    sql_scripts:                      
      - create-tables-ejbca-mysql.sql
      - create-index-ejbca.sql                 
```

### Example playbook
```yaml
- hosts: ejbca_servers
  roles:
      - role: fpki_roles_kfmariadb
```

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)