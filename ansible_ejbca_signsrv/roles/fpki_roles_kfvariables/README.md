Role Name
=========

The purpose of the fpki_roles_kfvariables role is have a single place to hold all the variables for the different Keyfactor EJBCA roles.  These roles tend to re-use the same variables, and rather have the roles define each variable independently, this role acts as a dependency for each of the other roles.  This role does not perform any tasks.

Requirements
------------

None.

Role Variables
--------------

Variables for the database role:

    mariadb_databases: 
      -   name: ejbca
    mariadb_users: 
      - name: "{{ kf_ejbca_db_username }}"
        password: "{{ kf_ejbca_vault_db_pass }}"
        priv: 'ejbca.*:ALL' 
    mariadb_vault_root_pw: 'changeMeInaVault'

    mariadb_configure_swappiness: true
    mariadb_swappiness: '10'
    mariadb_srv_socket: /var/lib/mysql/mysql.sock


    mariadb_custom_cnf: {}

Variables for the JBoss role:

    jboss_dir: "{{ jboss_install_dir }}/{{ jboss_name }}"
    jboss_name: jboss-eap-{{ jboss_version }}
    jboss_version: 7.4

    jboss_user: jboss-eap
    jboss_group: jboss-eap

    jboss_software_source: /media/software-dna/JBoss
    jboss_zip_file: jboss-eap-7.4.0.zip
    jboss_install_dir: /opt
    jboss_dir_mode: '0750'
    jboss_update_zip_file: jboss-eap-7.4.16-patch.zip
    jboss_patch_version: "JBoss EAP 7.4.16.GA"
    jboss_home: /opt/wildfly

    jboss_java_xms: 4096m
    jboss_java_xmx: 4096m
    jboss_dh_size: 3072

Keyfactor variables used throughout most other Keyfactor roles:

    kf_ejbca_home: /opt/ejbca
    kf_ejbca_user: jboss-eap
    kf_ejbca_group: jboss-eap
    kf_java_home: /usr/lib/jvm/java
    kf_jboss_pass_file: /usr/local/bin/wildfly
    kf_vault_nssdb_pass: ChangeItNowAndRecordIt
    kf_jboss_credentialstore: FipsCredentialStore
    kf_jboss_credentialstore_path: "{{ jboss_home }}/standalone/configuration/keystore"
    kf_jboss_credentialstore_file: "{{ jboss_home }}/standalone/configuration/keystore/jboss.jceks"
    kf_vault_db_pass: ChangeItNowAndRecordIt
    kf_db_reference: dbPassword # kf_db_reference is the reference to the credential in the JBoss credential Store.  

    kf_ejbca_db_username: ejbca
    kf_ejbca_vault_db_pass: ChangeItNowAndRecordIt

    kf_partition_name: junkpartition 
    kf_vault_partition_passphrase: FXM7-FtHM-7pFF-bNGC
    kf_dbProtection_key_label: "{{ ansible_hostname }}-dbProtect01"
    kf_install_dir: /opt
    kf_software_source: "/media/software-dna/Keyfactor-EJBCA/Software Files/Software Version"
    kf_ant_archive: apache-ant-1.10.14-bin.zip
    kf_ant_user: root
    kf_ant_group: root
    kf_ant_version: 1.10.14
    kf_ant_home: /opt/apache-ant
    kf_ejbca_version: ejbca_ee_8_2_0
    kf_ejbca_archive: ejbca_ee_8_2_0.zip
    kf_jdbc_driver: mariadb-java-client.jar
    kf_jdbc_driver_orig: mariadb-java-client-2.7.0.jar
    kf_vault_tmppw: fpki123FPKI123

    kf_RootCA: f-TLSRoot1
    kf_subCA: l-TLSISSUER5

Variables located in catoken.properties:

    kf_slotLabelType: SLOT_NUMBER
    kf_slotValue: 0

Variables located in cesecore.properties:

    kf_vault_password_encryption_key: ChangeItNowAndRecordIt
    kf_core_allow_external_dynamic_configuration: true
    kf_core_ca_serialnumberoctetsize: 20

    ####kf_core_disable_audit_log_to_db: false

Variables located in database.properties:

    kf_db_datasource_jndiname: ejbcads
    kf_db_database_useSeparateCertificateTable: true

Variables located in ejbca.properties:

    kf_cli_defaultusername: ejbca
    kf_vault_cli_defaultpassword: ejbca

Variables located in install.properties:

    kf_mgmtCaName: NewManagementCA
    kf_mgmtCaDN: CN=NewManagementCA,OU=PKI-Lab,O=U.S. Government,C=US
    # NOTE: 2.16.840.1.101.3.2.1.48.1 is an FPKI test policy OID
    kf_cas_policy: 2.16.840.1.101.3.2.1.48.1

Variables for Enable DB integrity protection:

    kf_databaseprotection_enablesign_AuditRecordData: true
    kf_databaseprotection_enableverify_AuditRecordData: true

Variables located in web.properties:

    kf_https_server_DN: OU=FPKI-LAB,o=U.S. Government,C=US
    kf_vault_superadminpassword: ejbca


Variables located in fpki_roles_kf_mgmtca:

    kf_SSLCertificateFile: /etc/pki/tls/certs/{{ ansible_fqdn }}.crt
    kf_SSLCertificateKeyFile: /etc/pki/tls/private/{{ ansible_fqdn }}.key
    kf_SSLCertificateChainFile: /etc/pki/tls/certs/managementca.crt
    kf_token_name: junktoken # NOTE: The token name is an EJBCA token name (similar to lunasa in RedHat)
    kf_defaultkey: "{{ ansible_hostname }}-MgmtDefaultKey001"
    kf_certsignkey: "{{ ansible_hostname }}-MgmtSignKey001"
    kf_crlsignkey: "{{ ansible_hostname }}-MgmtSignKey001"

    kf_keyenckey: "{{ ansible_hostname }}-MgmtDefaultKey001"
    kf_testkey: "{{ ansible_hostname }}-MgmtTestKey"
    kf_cdp: "http://{{ ansible_fqdn }}/crls/managementca.crl"

Variables located in eeprof-PeerClientEEProfile.yaml.j2 template:

    kf_org: "U.S. Government"
    kf_orgunit: fpki-lab

Variables located in create_user_and_web_certs.yml:

    kf_superman_dn: "CN={{ kf_superman_cn }},OU=fpki-lab,O=U.S. Government,C=US"
    kf_superman_cn: "Superman-l-tlsissuer5"
    kf_server_dn: "CN={{ ansible_fqdn }},OU=fpki-lab,O=U.S. Government,C=US"


Variables for SubCA, OCSP, and Peer Connectors:

    kf_subca_dn: "CN=Test US Federal TLS CA 5,O=U.S. Government,C=US"
    kf_serviceuri: "http://{{ ansible_fqdn }}:8080/ejbca/publicweb/status/ocsp"
    kf_va_publisher: "VA Publisher"
    kf_crl_expiration_period: 10d
    kf_crl_issue_interval: 12h
    kf_crl_overlap: 0m

subCA-OcspResponsePresigner.yaml.j2:

    kf_time_before: 30
    kf_time_unit: MINUTES
    kf_periodical_value: 2
    kf_periodical_unit: MINUTES

create_web_certs.yml:
    kf_server_domain: inf.fpki-lab.gov  # This is the domain portion of the server name after the hostname (e.g., l-tlsocsp.inf.fpki-lab.gov)

ca_client_auth_certs.yml:

    peer_deployment_folder: peerconfig
    peer_bindingkeypair_alias: "{{ ansible_hostname }}-PeerClientAuth"

RemoteAuthKeyBinding.yaml.j2:

    peer_keybinding_name: "{{ ansible_hostname }}-RemoteAuthKeyBinding"
    peer_keybinding_sigAlg: SHA384withRSA

global-peer-configuration.yaml.j2:

    hostvariable_peer_incoming: false
    hostvariable_peer_outgoing: false


first instance of variable in the main.yml file:

    subca_rootca_pem: f-tlsroot1.pem
    subca_rootca_dest: /opt/ejbca/deployment/f-tlsroot1.pem
    subca_deployment: "{{ kf_ejbca_home }}/deployment"

SubCA catoken.properties.j2:

    subca_defaultKey: "{{ ansible_hostname }}-{{ kf_subCA }}DefaultKey001"
    subca_certSignKey: "{{ ansible_hostname }}-{{ kf_subCA }}SignKey001"
    subca_crlSignKey: "{{ ansible_hostname }}-{{ kf_subCA }}SignKey001"


subCAconfig.yaml.j2 from the fpki_roles_kfocsp:

    ocsp_defaultKey: "{{ subca_defaultKey }}-{{ kf_subCA }}DefaultKey001"
    ocsp_certSignKey: "{{ subca_certSignKey }}-{{ kf_subCA }}SignKey001"
    ocsp_crlSignKey: "{{ subca_crlSignKey }}-{{ kf_subCA }}SignKey001"
    ocsp_dn: "{{ kf_subca_dn }}"



Dependencies
------------

None.

Example Playbook
----------------

If not using the dependency listed in meta/main.yml, the role could be used in a playbook along with another role:

    - hosts: servers
      roles:
         - fpki_roles_kfvariables
         - fpki_roles_kf_mgmtca

License
-------

TBD

Author Information
------------------

FPKIMA
