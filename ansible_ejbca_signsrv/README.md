# ansible_ejbca

An Ansible playbook that installs EJBCA Certificate Authorities, Registration Authorities, Validation Authorities. The Certificate Authorities are signed by an Non-EJBCA External Root CA.

## Scope
The following deployment is supported:

- Deploy Issuing CAs Nodes with a local Management CA
- Import Issuing CA signed by Third-Party Root CA
- Deploy External Registration Authority
- Deploy External Validation Authority

## Requirements

- Internet Access (required for downloading Wildfly, JDBC driver, HSM client, etc.)
- Access to a repository containing packages, likely on the internet.
- A recent version of [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
- A web respository that has the Community EJBCA or SignServer, or the enterprise version of EJBCA (Full, RA & VA builds) version to download. Community can be downloaded from GitHub if you would like to keep the zip release local
- A host with Ansible, AWX, or Ansible Tower installed from where to run the Ansible playbook
- A Linux host that EJBCA shall be installed on, reachable from the Ansible host using SSH with configured SSH keys for SSH agent based login (preferred method) or password, and the user with ability to become root using sudo
- The target host needs the configured hostname in DNS or hostsfile for Apache to startup properly, i.e.

### Variables
There are numerous variables for this playbook. These variables are set in `group_vars` and the `host_vars`. Reference the vars files for the settings used to deploy.

### Roles
The following roles are required meet the defined scope:

- ansible-ejbca-import-ext-ca
- ansible-ejbca-init-ca
- ansible-ejbca-certreq-cli
- ansible-ejbca-conf-ca-exts
- ansible-ejbca-crypto-token
- ansible-ejbca-ctb-keygen
- ansible-ejbca-deploy-pki-ca-node-mgmt-ca
- ansible-ejbca-deploy-pki-ext-signed-sub-1
- ansible-ejbca-deploy-pki-ext-signed-sub-3
- ansible-ejbca-deploy-pki-ext-signed-sub-4
- ansible-ejbca-key-binding
- ansible-ejbca-peer-connector
- ansible-ejbca-peer-in
- ansible-ejbca-pkc11-client
- ansible-ejbca-prep
- ansible-ejbca-ra-ee
- ansible-ejbca-va-ee
- ansible-ejbca-restart-wildfly
- ansible-ejbca-roles
- ansible-ejbca-wildfly
- ansible-httpd
- ansible-hostname
- ansible-role-mariadb

## Playbooks
Run the following playbooks in the order listed to meet the defined scope:

### Deploy Initial Issuing CA node and Create Management CA
1. Edit _group_vars/eeCaServers.yml_, _host_vars/ca01.yml_, and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l eeCaServers,ca01 deployExtRootCaForSubPart1.yml --ask-become-pass
```

### Initialize Issuing CAs and Generate CSRs for signing
1. Edit _group_vars/eeCaServers.yml_, _host_vars/ca01.yml_, and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l eeCaServers,ca01 deployExtRootCaForSubPart3.yml --ask-become-pass
```

### Import Externally Signed CA Certificates into EJBCA
1. Edit _group_vars/eeCaServers.yml_, _host_vars/ca01.yml_, and _inventory_.
2. SCP externally signed CA certs to the /var/tmp directory on the initial ca node
3. Run:

```bash
ansible-playbook -i inventory -l eeCaServers,ca01 deployExtRootCaForSubPart5.yml --ask-become-pass
```

### Deploy Additional CA Node(s)
1. Edit _group_vars/eeCaServers.yml_, _host_vars/ca02.yml_, and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l ejbcaCaAddNodes,ca02 deployAddCaNode.yml
```

### Deploy External RA node
1. Edit _group_vars/eeRaServers.yml_, _group_vars/pkiTlsCerts.yml_, _host_vars/ra01.yml_, and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l ra01,pkiTlsCerts deployRa.yml --ask-become-pass
```

### Deploy External VA node
1. Edit _group_vars/eeVaServers.yml_, _group_vars/pkiTlsCerts.yml_, _group_vars/pkiCsrCerts.yml_, _host_vars/va01.yml_, and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l va01,pkiTlsCerts,pkiCsrCerts deployVa.yml --ask-become-pass
```

### Upgrade EJBCA Community or Enterprise
1. Edit _group_vars/ceServers.yml_ or _group_vars/eeCaServers.yml_, _group_vars/eeRaServers.yml_, _group_vars/eeVaServers.yml_ and _inventory_.
2. Run:

```bash
ansible-playbook -i inventory -l upgradeEeServers
```

### Use Ansible Vault

Create a password file protected with Ansible Vault:

```bash
touch passwords/custom_enc_ca_vars.yml
ansible-vault create passwords/custom_enc_ca_vars.yml
```

Edit the password file to add/remove variables:

```bash
ansible-vault edit passwords/custom_enc_ca_vars.yml
```

Use the Ansible Vault password file:

```bash
ansible-playbook --ask-vault-pass -i inventory -e @passwords/custom_enc_ca_vars.yml deployCa.yml
```

## Ansible plays

### deployExtRootCaForSubPart1.yml (Enterprise)
Installs and configures EJBCA with a management CA. A TLS certificate is created for the Root CA Node and downloaded the Ansible controller.  The stack includes Java 11, Apache HTTPD, Maria DB, SoftHSM, & Wildfly. The playbook has the ability to set up other HSMs.

### deployExtRootCaForSubPart3.yml (Enterprise)
Creates a sub CA in EJBCA issuing CA node, downloads the CSR to the Ansible controller.

### deployExtRootCaForSubPart5.yml (Enterprise)
Uploads the signed sub CA certificate to the issuing EJBCA node, imports the certificate, and then does post configuration tasks (Key bindings, peering, publishers, etc).

### deployAddCaNode.yml (Enterprise)
Installs and configures EJBCA as an Issuing CA node that connects to an already existing CA Database. 

### deployRa.yml (Enterprise)
Installs and configures EJBCA as an External RA. The play can configure the protocols when not using the RA variant which does not have protocol configuration using the CLI yet. The stack includes Java 11, Apache HTTPD, Maraia DB, SoftHSM, & Wildfly.

### deployVa.yml (Enterprise)
Installs and configures EJBCA as an External Validation Authority. The play configures OCSP signing certificates for the management, Root and Sub CA's. The stack includes Java 11, Apache HTTPD, Maraia DB, SoftHSM, & Wildfly.


### upgradeEjbca.yml (Community & Enterprise)
Upgrade EJBCA community and enterprise versions to a new version. EJBCA is downloaded from an internal web repo or can be downloaded from GitHub. The zip release is unarchived, compiled, and deployed.

### Documentation 
Also see a [full documentation of EJBCA](https://doc.primekey.com/ejbca) on how to further configure/manage EJBCA.


## Compatibility

This role has been tested on these:

|container|tags|
|---------|----|
|el| 8|


The minimum version of Ansible required is 2.9 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.

## Exceptions

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| TBD | TBD |

## Installation Notes

1. Using a Alma or Rocky 8 VM to install onto, installing python3 from yum makes /usr/bin/python3 available, while Ansible by default looks for /usr/bin/python.

The following can been set in the `group_vars/all.yml` that will be used for running plays, e.g. deployCa.yml or deployRa.yml, deployCeSS.yml

```yaml
    ansible_python_interpreter: /usr/bin/python3
```

2. Also seen on RedHat derivitive (CentOS, Alma, Rocky) is that Apache enables TLSv1.3 by default, and FireFox does not work with client certificate authentication using TLSv1.3. This results in EJBCA Admin UI being unreachable. The TLS config in Apache in available on the target, after the installation, in /etc/httpd/conf.d/ssl.conf
The setting in question is _SSLProtocol -all +TLSv1.2_ and You can enable this setting in the playbook in the file ./roles/ansible-ejbca-httpd/templates/ssl2.conf.j2.

3. The superadmin keystore, SkyrimSuperAdministrator.p12 file ends up in ~/Desktop in the host where you run the ansible-playbook command.

## License

LGPL v2.1 or later. See [LICENSE](./LICENSE).

## Author Information
- Keyfactor Community Team

