# Ansible role `fpki_roles_kfvariables`

The purpose of the fpki_roles_kfvariables role is have a single place to hold all the variables for the different Keyfactor EJBCA roles.  These roles tend to re-use the same variables, and rather have the roles define each variable independently, this role acts as a dependency for each of the other roles.  This role does not perform any tasks.

## Requirements

None

## Role Variables

Variables for the software role:

    kf_software_root_dir: /opt
    kf_software_media_dir: /opt/media/software-dna
    kf_software_common_pkgs:
      - unzip
      - java-11-openjdk-devel
      - nss-tools
      - wget
      - python3-libsemanage
      - httpd
      - mod_ssl
      - firewalld
      - ca-certificates

    kf_software_binaries: 
      - "{{ kf_software_ejbca }}"
      - "{{ kf_software_ant }}"
      - "{{ kf_software_jboss }}"

    kf_software_ejbca:
      name: ejbca
      home: "{{ kf_ejbca_home }}"
      path: "{{ kf_software_root_dir }}/{{ kf_ejbca_version }}"
      filename: "{{ kf_ejbca_version }}.zip"
      source_dir: "{{ kf_software_media_dir }}/Keyfactor-Ejbca/Software Files/Software Version"
      symlink_create: true
      dependencies:
        - mariadb
        - mariadb-backup
        - mariadb-common
        - mariadb-server
        - mariadb-server-galera
        - python3-PyMySQL
      user: "{{ kf_ejbca_user }}"
      group: "{{ kf_ejbca_group }}"

    kf_software_ant:
      name: ant
      home: "{{ kf_ant_home }}"
      path: "{{ kf_software_root_dir }}/apache-ant-{{ kf_ant_version }}"
      filename: apache-ant-{{ kf_ant_version }}-bin.tar.gz
      source_dir: "{{ kf_software_media_dir }}/Keyfactor-Ejbca/Software Files/Software Version"
      symlink_create: true
      symlink: 
        src: "{{ kf_software_root_dir }}/apache-ant-{{ kf_ant_version }}/bin/ant"
        dest: /usr/bin/ant

    kf_software_jboss:
      name: jboss
      home: "{{ jboss_home }}"
      path: "{{ kf_software_root_dir }}/jboss-eap-{{ jboss_version }}"
      dir_mode: '0750'
      filename: jboss-eap-7.4.0.zip
      update_zip_file: jboss-eap-7.4.16-patch.zip
      path_version: JBoss EAP 7.4.16.GA
      source_dir: "{{ kf_software_media_dir }}/JBoss"
      symlink_create: true
      dependencies:
        - java-11-openjdk-headless.x86_64 
        - nss-tools
      user: "{{ jboss_user }}"
      group: "{{ jboss_group }}"


Variables for the MariaDB role:

    mariadb_root_password: "{{ vault_mariadb_root_password|default('ejbca') }}"
    mariadb_configure_swappiness: true
    mariadb_swappiness: 10
    mariadb_srv_socket: /var/lib/mysql/mysql.sock
    mariadb_custom_cnf: {}
    mariadb_databases: 
      ejbca: 
        name: ejbca
        user: ejbca-usr
        password: "{{ kf_ejbca_vault_db_pass|default('ejbca') }}"
        priv: "ejbca.*:ALL"        
        sql_scripts:                      
          - create-tables-ejbca-mysql.sql
          - create-index-ejbca.sql

Variables for the JBoss role:

    jboss_user: "{{ kf_user }}"
    jboss_group: "{{ kf_group }}"
    jboss_home: "{{ app_root_dir }}/jboss"
    jboss_name: jboss-eap-{{ jboss_version }}
    jboss_java_xms: 2048m
    jboss_java_xmx: 2048m
    jboss_dh_size: 3072
    jboss_remove_dirs:
      - docs
      - domain
      - welcome-content
    jboss_version: 7.4

Variables for the EJBCA Prep role:

    kf_java_home: /usr/lib/jvm/java
    kf_jboss_pass_file: /usr/local/bin/wildfly
    kf_jboss_credentialstore: FipsCredentialStore
    kf_jboss_credentialstore_path: "{{ jboss_home }}/standalone/configuration/keystore"
    kf_jboss_credentialstore_file: "{{ jboss_home }}/standalone/configuration/keystore/jboss.jceks"
    kf_dbProtection_key_label: dbProtectionKey

    # cesecore.properties
    kf_properties_cesecore_allow_external_dynamic_configuration: true
    kf_properties_cesecore_password_encryption_key: "{{ kf_vault_password_encryption_key|default('') }}"
    kf_properties_cesecore_ca_serialnumberoctetsize: 20

    # database.properties
    kf_properties_database_jndi_name: ejbcads
    kf_properties_database_use_separate_certificate_table: true

    # ejbca.properties
    kf_properties_ejbca_cli_default_username: ejbca
    kf_properties_ejbca_cli_default_password: "{{ kf_vault_cli_defaultpassword|default('ejbca') }}"

Keyfactor variables used throughout most other Keyfactor roles:

    kf_user: jboss-eap
    kf_group: jboss-eap
    kf_org: U.S. Government
    kf_org_unit: fpki-lab
    kf_dn: "OU={{ kf_org_unit }},O={{ kf_org }},C=US"
    kf_server_domain: inf.fpki-lab.gov
    kf_remote_src_dir: "{{ playbook_dir }}/transfer"

    # HSM
    kf_token_name: junktoken
    kf_partition_name: junkpartition 
    kf_vault_partition_passphrase: foo123
    kf_dbProtection_key_label: dbProtectionKey

    # EJBCA
    kf_ejbca_user: "{{ kf_user }}"
    kf_ejbca_group: "{{ kf_group }}"
    kf_ejbca_home: "{{ kf_software_root_dir }}/ejbca"
    kf_ejbca_create_home: true
    kf_ejbca_version: ejbca_ee_8_2_0_3

    # Apache Ant
    kf_ant_user: root
    kf_ant_group: root
    kf_ant_home: "{{ kf_software_root_dir }}/apache-ant"
    kf_ant_version: 1.10.14

    # JDBC
    kf_jdbc_driver: mariadb-java-client.jar
    kf_jdbc_driver_source: "{{ kf_software_media_dir }}/Keyfactor-Ejbca/Software Files/Software Version/mariadb-java-client-3.0.8.jar"


Variables for Certificate Authorities:

    kf_cas_policy: 2.16.840.1.101.3.2.1.48.1
    kf_cas_root: IT-Root-CA
    kf_cas_root_pem: "{{ kf_cas_root }}.pem"
    kf_cas_active:
      - "{{ kf_cas_mgmt }}"
      - "{{ kf_cas_sub }}"
    kf_cas_mgmt:
      name: NewManagementCA
      dn: CN=NewManagementCA,OU=PKI-Lab,O=U.S. Government,C=US
      cdp: "http://{{ ansible_fqdn }}/crls/managementca.crl"
      profile: MgmtCACertProfile
      token: "{{ kf_token_name }}"
      ca_keys:
        defaultkey: "{{ ansible_hostname }}-MgmtDefaultKey001"
        certsignkey: "{{ ansible_hostname }}-MgmtSignKey001"
        crlsignkey: "{{ ansible_hostname }}-MgmtSignKey001"
        keyenckey: "{{ ansible_hostname }}-MgmtDefaultKey001"
        testkey: "{{ ansible_hostname }}-MgmtTestKey"

    kf_cas_sub:
      name: l-TLSISSUER4
      dn: CN=l-TLSISSUER4,OU=PKI-Lab,O=U.S. Government,C=US
      externalcachain: "{{ kf_ejbca_home }}/certificates/{{ kf_cas_root }}.pem"
      crl_expiry_period: 10d
      crl_issue_interval: 12h
      crl_overlap: 0h
      ocsp_service_uri: http://{{ ansible_fqdn }}:8080/ejbca/publicweb/status/ocsp
      token: "{{ kf_token_name }}"
      ca_keys:
        defaultkey: "{{ ansible_hostname }}-l-TLSISSUER5DefaultKey001"
        certsignkey: "{{ ansible_hostname }}-l-TLSISSUER5SignKey001"
        crlsignkey: "{{ ansible_hostname }}-l-TLSISSUER5SignKey001"

Variables for Peering:

    kf_peering_ca_role: CA-Peers
    kf_peering_ra_role: RA-Peers
    kf_peering_remote_auth:
      name: "{{ ansible_hostname }}-RemoteAuthKeyBinding"
      dn: CN={{ ansible_fqdn }},OU={{ kf_org_unit }},O={{ kf_org }}, C=US
      password: "{{ kf_vault_tmppw|default('foo123') }}"
      caname: "{{ kf_cas_mgmt.name }}"
      certprofile: MgmtPeerClient
      eeprofile: MgmtPeerClientEEProfilef
      crypto_token: "{{ kf_token_name }}"
      alias: "{{ ansible_hostname }}-PeerClientAuth"
      keyspec: 3072
      sigalg: SHA384withRSA

Variables for EJBCA Super Administrators:

    kf_superadmins:
      initial:
        cn: Superman-Admin
        dn: CN=Superman-Admin,OU={{ kf_org_unit }},O={{ kf_org }},C=US
        password: "{{ kf_vault_superadminpassword|default('foo123') }}"
        role: Super Administrator Role
        caname: "{{ kf_cas_mgmt.name }}"
        token: P12

Variables for EJBCA Services:

    kf_service_presigner: OcspResponsePresigner
    kf_service_publisher_queue: PublisherQueue

Variables for Configdump:

    configdump_files:
      initial:
        certification-authorities:
          - ca-MgmtCA.yaml.j2
          - ca-SubCA.yaml.j2
        certificate-profiles:
          - cp-AdminEndEntity.yaml.j2
          - cp-MgmtCa.yaml.j2
          - cp-MgmtPeerClient.yaml.j2
          - cp-PeerClient.yaml.j2
          - cp-SubCaPeerClient.yaml.j2
          - cp-TlsServer.yaml.j2
        end-entity-profiles:
          - eep-AdminEndEntity.yaml.j2
          - eep-MgmtPeerClient.yaml.j2
          - eep-PeerClient.yaml.j2
          - eep-SubCaPeerClient.yaml.j2
          - eep-TlsServer.yaml.j2
        ocsp-configuration:
          - ocsp-Configuration.yaml.j2
        services:
          - svc-CrlUpdater.yaml.j2
          - svc-HsmKeepAlive.yaml.j2
        admin-roles:
          - role-RaPeer.yaml.j2

      post_peers:
        acme-config:
          - acme-ECC.yaml.j2
          - acme-ECCdns01.yaml.j2
          - acme-RSA.yaml.j2
          - acme-RSAdns01.yaml.j2
          - global-acme-configuration.yaml.j2
        certificate-profiles:
          - cp-PublicTrust90DayECCServerClientAuthProfile.yaml.j2
          - cp-PublicTrust90DayRSAServerClientAuthProfile.yaml.j2
        end-entity-profiles:
          - eep-PublicTrust90DayTLSECC-DNS01.yaml.j2
          - eep-PublicTrust90DayTLSECC.yaml.j2
          - eep-PublicTrust90DayTLSRSA-DNS01.yaml.j2
          - eep-PublicTrust90DayTLSRSA.yaml.j2
        services:
          - svc-PublishQueueProcess.yaml.j2
          - svc-OcspResponsePresigner.yaml.j2
        validators:
          - validator-CAA.yaml.j2
          - validator-Domain.yaml.j2
          - validator-ECCkey.yaml.j2
          - validator-RSAkey.yaml.j2
          - validator-Zlint.yaml.j2

      peer_servers:
        admin-roles:
          - role-CaPeer.yaml.j2
        available-protocols:
          - protocols-peers.yaml.j2

## Dependencies

None

## Example Playbook

If not using the dependency listed in meta/main.yml, the role could be used in a playbook along with another role:

    - hosts: servers
      roles:
         - fpki_roles_kfvariables
         - fpki_roles_kf_mgmtca

## License

TBD

## Author Information

FPKIMA
