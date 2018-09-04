# ahuffman.satellite6_bootstrap

To provide a method to painlessly migrate Satellite 5 clients to Satellite 6 by making use of Ansible.  This role also provides for the initial registration of clients to Satellite6.  The role ensures all RHN (Satellite5) specific components are removed as part of the process.

## Role Variables
| Variable Name | Required | Description | Default Value | Variable Type |
| --- | :---: | :---: | --- | :---: |
| sat6_server | yes | Hostname of the Satellite6 server. | "" | string |
| sat6_activation_key | yes | Activation key to use when registering the host with Satellite6. | "" | string |
| sat6_organization | yes | Organization to register the host in on the Satellite6 server. | "" | string |
| sat6_enable_repos | no | List of repositories to enable after Satellite6.  registration is complete. | [] | list |
| sat6_remote_execution | no | Place a file named `id_rsa_foreman_proxy.pub` in this role's `files` directory.  You can retrieve this file from your Satellite6 server. | False | boolean |    
| sat6_auth_key_path | no | Path to your authorized keys file if it is not /home/<user>/.ssh/authorized_keys. | "" | string |
| sat6_puppet | no | Whether or not to perform Puppet installation and configuration. | False | boolean |

## Example Playbook

    - hosts: "servers"
      roles:
        - role: "ahuffman.satellite6_bootsrap"
          sat6_server: "mysat6.foo.bar"
          sat6_activation_key: "mykey1"
          sat6_organization: "myorg1"
          sat6_enable_repos:
            - "rhel-7-server-optional-rpms"
          sat6_remote_execution: True
          sat6_puppet: True

## License
[MIT](LICENSE)

## Author
[Andrew J. Huffman](https://github.com/ahuffman)
