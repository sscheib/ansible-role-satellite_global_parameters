
satellite_global_parameters
=========

This role creates Global Parameters in a Red Hat Satellite. 

It makes use of the Red Hat certified collection [`redhat.satellite`](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/docs/).
To use the certified collection `redhat.satellite` you need to be a Red Hat subscriber. If you don't own any subscriptions, you can make use of
[Red Hat's Developer Subscription](https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux) which is provided at no cost by Red Hat.

You can also make use of the upstream collection [`theforeman.foreman`](https://docs.ansible.com/ansible/latest/collections/theforeman/foreman/index.html), but you'd need to change the module names from `redhat.satellite` to `theforeman.foreman` - but I have *not* tested this.

Requirements
------------

This role requires the `redhat.satellite` collection, which is specified via `collections/requirements.yml`.

Role Variables
--------------
| variable                                     | default                      | required | description                                                                    |
| :---------------------------------           | :--------------------------- | :------- | :----------------------------------------------------------------------------- |
| `sat_global_parameters`                      | unset                        | true     | Global Parameters to create                                                    |
| `satellite_username`                         | unset                        | true[^1] | Username to authenticate with against the Satellite API                        |
| `satellite_password`                         | unset                        | true[^1] | Password of the user to authenticate with against the Satellite API            |
| `satellite_server_url`                       | unset                        | true[^1] | URL to the Satellite API (including http/s://)                                 |
| `satellite_validate_certs`                   | `true`                       | false    | Whether to validate certificates when connecting to the Satellite API          |
| `sat_allowed_parameter_types`                | check `defaults/main.yml`    | false    | Allowed parameter types to validate against (does usually not need changing)   | 
| `sat_quiet_assert`                           | `true`                       | false    | Whether to quiet assert                                                        |

## variable `sat_global_parameters`

Find below an extended example of the variable `sat_global_parameters`:
```
sat_global_parameters:
  - name: 'p-ansible_provisioning'
    parameter_type: 'boolean'
    hidden_value: false
    value: true

  - name: 'p-ansible_job_template_id'
    parameter_type: 'integer'
    hidden_value: false
    value: 10

  - name: 'p-ansible_controller_host'
    hidden_value: false
    value: 'https://controller.example.com'

  - name: 'p-ansible_inventory_id'
    parameter_type: 'integer'
    hidden_value: false
    value: 6

  - name: 'p-ansible_ssh_user'
    hidden_value: true
    value: !vault |
            $ANSIBLE_VAULT;1.1;AES256
            [..]
```

Required attributes are `name` and `value`. If `parameter_type` is not specified the module `redhat.satellite.global_parameter` will use `string` as default.

[^1]: This role also supports passing the Satellite URL, username and password via environment variables. Please check `vars/main.yml` for the exact variables to use.
```

Dependencies
------------

None

Example Playbook
----------------

```
---
- name: 'Create Global Parameters in Red Hat Satellite'
  hosts: 'all'
  gather_facts: false
  roles:
    - role: 'sat_global_parameters'
      vars:
        satellite_server_url: 'https://satellite.example.com'
        satellite_username: 'steffen'
        satellite_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          [..]
        satellite_validate_certs: true
        sat_quiet_assert: true
        satellite_global_parameters:
          - name: 'p-ansible_provisioning'
            parameter_type: 'boolean'
            hidden_value: false
            value: true

          - name: 'p-ansible_job_template_id'
            parameter_type: 'integer'
            hidden_value: false
            value: 1337

          - name: 'p-ansible_controller_host'
            hidden_value: false
            value: 'https://controller.example.com'

          - name: 'p-ansible_inventory_id'
            parameter_type: 'integer'
            hidden_value: false
            value: 1337

          - name: 'p-ansible_ssh_user'
            hidden_value: true
            value: !vault |
                    $ANSIBLE_VAULT;1.1;AES256
                    [..]
...
```

License
-------

GPL-2.0-or-later
