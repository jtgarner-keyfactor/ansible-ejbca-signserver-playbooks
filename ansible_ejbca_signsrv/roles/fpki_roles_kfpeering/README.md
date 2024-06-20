fpki_roles_kfpeerconnector
=========

This role configures peer connections between an EJBCA instance with a CA, and an EJBCA instance that is either a VA or RA.

Requirements
------------

This role assumes that at least a Subordinate CA and OCSP server have been configured.  The intent for future use is to also have an RA server configured.  The server that is the Subordinate CA must be in a group named 'certificate_authority'.

Role Variables
--------------

All role variables should be set in the fpki_roles_kfvariables role.

Dependencies
------------

fpki_roles_kfocsp

dependencies:
  - role: fpki_roles_kfvariables


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: l-tlsissuer1, l-tlsocsp1
      roles:
         - fpki_roles_kfpeerconnector

License
-------

TBD

Author Information
------------------

FPKIMA
