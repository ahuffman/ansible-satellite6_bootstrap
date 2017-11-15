# ahuffman.satellite6_migrate

To provide a method to painlessly move Satellite 5 clients to Satellite 6 by making use of Ansible.

## Role Variables
`sat6_server`: **Required** - Hostname of the Satellite6 server.  
`sat6_activation_key`: **Required** - Activation key to use when registering the host with Satellite6.  
`sat6_organization`: **Required** - Organization to register the host in on the Satellite6 server.  
`sat6_enable_repos`: [] **Optional** - List of repositories to enable after Satellite6 registration is complete.  Defaults to an empty list.  
`sat6_remote_execution`: **Optional** - true | false - Defaults to false.  Place a file named `id_rsa_foreman_proxy.pub` in this role's `files` directory.  You can retrieve this file from your Satellite6 server.    
`sat6_puppet`: **Optional** - true | false - Defaults to false.  Whether or not to perform Puppet installation and configuration.  

## Example Playbook

    - hosts: servers
      vars:
        sat6_server: mysat6.foo.bar
        sat6_activation_key: mykey1
        sat6_organization: myorg1
        sat6_enable_repos:
          - rhel-7-server-optional-rpms
        sat6_remote_execution: true
        sat6_puppet: true
      roles:
         - { role: ahuffman.satellite6_migrate }

## License
[MIT](LICENSE)

## Author
[Andrew J. Huffman](https://github.com/ahuffman)
