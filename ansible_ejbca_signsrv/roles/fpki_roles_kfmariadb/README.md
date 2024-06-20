# Ansible role `fpki_roles_kfmariadb`

- Remove unsafe defaults:
    - set database root password (remark that once set, this role is unable to *change* the database root password)
    - remove anonymous users
    - remove test database
- Create users and databases
- Uses variables nested in a dictionary to allow for the creation of multiple databases without using a list of dictionaries.

**Remarks**
Installing MariaDB should be done through the `fpki_roles_kfsoftware` role.

## Requirements

No specific requirements

## Role Variables

### Basic Configuration

This roles requires a dictionary of values to be declared when include this role in a play.

| Variable                        | Required    | Default                      | Comments                                                                                             |
| :---                            | :---        | :---                         | :---                                                                                                 |
| `mariadb_root_password`         | No          | ejbca                        | Root user password to set during initial configuration.                                              |
| `mariadb_configure_swappiness`  | No          | true                         | Switch for configuring "swappiness".                                                                 |
| `mariadb_swappiness`            | No          | 10                           | "Swappiness" value. System default is 60. A value of 0 means that swapping out processes is avoided. |
| `mariadb_srv_socket`            | No          | '/var/lib/mysql/mysql.sock'  | Server socket for connecting to the database.                                                        |   
| `mariadb_databases`             | No          | {}                           | Dictionary containing the databases to be created.                                                   |

### MariaDB Database Dictionary

A Database dictionary consists of the following variables

### Schema
```yaml
mariadb_databases:
  ejbca:                              
    name: ejbca                       # Database anme
    users:                            # Dictionary of users to add to the database
      ejbca-usr:                      # User name
        password: ejbca               # User password
        priv: "ejbca.*:ALL"           # Database privleges
    protection_key:                   # Dictionary of key values to add to the database
      label: dbProtectionKey          # Alias label
      size: 4096                      # Size
      type: RSA                       # Type: RSA, ECDSA, etc.
      usage: SIGN_ENCRYPT             # CKA attribute
    sql_scripts:                      # List of sql scripts to apply on the database
      - create-tables-ejbca-mysql.sql
      - create-index-ejbca.sql
```

### Examples
#### Single Database and User
```yaml
mariadb_databases:
  ejbca:     
    name: ejbca
    users:                            
      ejbca-usr:                                
        password: ejbca      
        priv: "ejbca.*:ALL"         
    protection_key:                   
      label: dbProtectionKey          
      size: 4096                      
      type: RSA                       
      usage: SIGN_ENCRYPT             
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
    users:                            
      ejbca-usr:                                   
        password: ejbca               
    protection_key:                   
      label: dbProtectionKey          
      size: 4096                      
      type: RSA                       
      usage: SIGN_ENCRYPT             
    sql_scripts:                      
      - create-tables-ejbca-mysql.sql
      - create-index-ejbca.sql

  # No database protection or SQL Scripts
  ejbca-peers:
    name: ejbca_peers
    users:
      va-usr:         
        password: ejbca               
      ra-usr:                              
        password: ejbca                        
```

## Example playbooks

```yaml
- hosts: ejbca_servers
  become: true
  tasks:

    - name: Deploy MariaDB
      ansible.builtin.include_role:
        name: fpki_roles_kfmariadb
```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)