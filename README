# MetaCentrum Pulsar Ansible Playbook

## requirements
You need to have `ansible`. Python virtualenv is a recommended way to its installation.

## Executing the playbook

Generate ssh key with `$ ssh-keygen -t ed25519`

- If you want to run the playbook through github action you need to add your key to your github profile.
- For executing this from a local machine you can forward your sshkey to the host.

## create vars file for your host

For the purposes below the `<YOUR_HOST>` can be e.g. `galaxy-qa2.galaxy.cloud.e-infra.cz`

Create `host_vars/<YOUR_HOST>/vars.yml`. It should contain the following vars:

```
pulsar:
  user_name: galaxy-qa1
  uid:  11688
  gid: 10000
  group: meta
  nfs_home: "brno11-elixir"
  nfs_prefix: pulsar-qa2

install_nfs_conda: false

rabbitmq_hostname: "galaxy-qa2.galaxy.cloud.e-infra.cz"
rabbitmq_vhost: "pulsar"
rabbitmq_user: "{{ pulsar.user_name }}"
rabbitmq_port: 5671
```

note: the same service user (`galaxy-qa1`) is used for both `pulsar-qa1` and `pulsar-qa2`

## create and fill ansible vault

generate password with `$ openssl rand -base64 24 > .vault-password.txt`

create vault for your host `ansible-vault create host_vars/<YOUR_HOST>/secret.yml`

The vault should contain the following vars:
- `rabbitmq_password`: password for the messaging service ran by the galaxy server
- `krb5_user_keytab`: keberos keytab for the service user, e.g. in the form of `"{{ 'ENCODED_KEYTAB' | b64decode }}"`
- `krb5_nfs_keytab`: keberos keytab for the service user NFS access, e.g. in the form of `"{{ 'ENCODED_KEYTAB' | b64decode }}"`

## running playbook
`$ ansible-playbook --limit <YOUR_HOST> pulsar.yml`

## custom role modifications

in `roles/galaxyproject.pulsar/tasks/paths.yml` this modification is present
```
    - name: Create Pulsar user-owned directories
      file:
        path: "{{ item }}"
        state: directory
        #owner: "{{ __pulsar_user_name }}"
        #group: "{{ __pulsar_user_group }}"
        #mode: "{{ __pulsar_dir_perms }}"
      with_items: "{{ pulsar_dirs }}"
      become: yes
      become_user: "{{ __pulsar_user_name }}"
```
