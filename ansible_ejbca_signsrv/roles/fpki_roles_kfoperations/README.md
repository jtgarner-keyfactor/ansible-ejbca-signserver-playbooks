# Ansible role `fpki_roles_kfoperations`

Common EJBCA operations intended to be called from a playbook using a loop. Tasks include

- Adding to Role
- Create Crypto Token and Keys
- Create Key Binding
- Manage an existing, or new, End Entity and issue a certificate

## Requirements

EJBCA needs to be configured with the `fpki_roles_kfeprep` role.

## Role Variables

All role variables should be set in the `fpki_roles_kfvariables` role.

## Dependencies

None.

## Examples
### Adding to Role

``` yaml
- name: Add Super Administrator to role
  loop: 
    - "{{ kf_superadmins.initial }}" # automatically converts dict to list
  loop_control:
    label: "{{ item.cn }}"
  ansible.builtin.include_role:
    name: fpki_roles_kfoperations
    tasks_from: add_to_role.yml
    apply:
      become_user: "{{ kf_ejbca_user }}"
```

### Create key binding

``` yaml
- name: Create Remote Authentication key binding
  when: inventory_hostname in groups['certificate_authorities'][0]
  loop: 
    - "{{ kf_peering_remote_auth }}" # automatically converts dict to list
  loop_control:
    label: "{{ item.name }}"
  ansible.builtin.include_role:
    name: fpki_roles_kfoperations
    tasks_from: create_key_binding.yml
    apply:
      become_user: "{{ kf_ejbca_user }}"
```

### Create crypto tokens

- Define the `key_objects` dictionary when called the task. The purpose of this is define different key dictionaries between crypoto token types.

``` yaml
  - name: Create crypto token and key
    loop:
      - "{{ kf_cas_mgmt }}"
    loop_control:
      label: "{{ item.name }}"
    vars:
      key_objects: "{{ item.ca_keys }}"
    ansible.builtin.include_role:
      name: fpki_roles_kfoperations
      tasks_from: create_crypto_tokens.yml
```

### Manage End Entity

``` yaml
- name: Create Super Administrator end entity and certificate
  loop:
    - "{{ kf_superadmins.initial }}" # automatically converts dict to list
  loop_control:
    label: "{{ item.cn }}"
  ansible.builtin.include_role:
    name: fpki_roles_kfoperations
    tasks_from: manage_end_entity.yml
    apply:
      become_user: "{{ kf_ejbca_user }}"

```

## Dependencies

No dependencies.

## License

BSD

## Contributors

- [Jamie Garner](https://github.com/jtgarner-keyfactor)