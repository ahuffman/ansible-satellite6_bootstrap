# ahuffman.satellite6_bootstrap

To provide a method to painlessly migrate Satellite 5 clients to Satellite 6 by making use of Ansible.  This role also provides for the initial registration of clients to Satellite6 or for re-registration of clients from an existing Satellite6 server.  The role ensures all RHN (Satellite5) specific components are removed as part of the process.

## Role Variables
The following variables are set in the role's `defaults/main.yml` file:  

| Variable Name | Required | Description | Default Value | Variable Type |
| --- | :---: | :---: | --- | :---: |
| sat6_server | yes | Hostname of the Satellite6 server. This can also be a Capsule server.| "" | string |
| sat6_organization | yes | Organization to register the host in on the Satellite6 server. | "" | string |
| sat6_activation_key | yes | Activation key to use when registering the host with Satellite6. | "" | string |
| sat6_enable_repos | no | List of repositories to enable after Satellite6.  registration is complete. | [] | list |
| sat6_remote_execution | no | Place a file named `[sat6_server]-id_rsa_foreman_proxy.pub` in this role's `files` directory.  You can retrieve this file from your Satellite6 server. Please note that the filename prefix should match your Satellite6 or Capsule server name set with the `sat6_server` variable| False | boolean |
| sat6_remote_execution_user | no | User account to install the Satellite6 remote execution key for. | "root" | string |
| sat6_remote_execution_auth_key_path | no | Path to your authorized keys file if it is not /home/<user>/.ssh/authorized_keys. | "" | string |
| sat6_puppet | no | Whether or not to perform Puppet installation and configuration. | False | boolean |
| sat6_puppet_environment | no | Puppet environment to configure the client's puppet agent with. | "production" | string |
| sat6_force_puppet | no | Whether or not to force an uninstall and cleanup of a client's puppet agent and configuration. | False | boolean |
| sat6_update_client | no | Whether or not to fully update (patch) the client being registered as part of the process. | False | boolean |
| sat6_force_katello | no | Whether or not to force the uninstall and cleanup of the client's katello agent components. | False | boolean |
| sat6_https | no | Whether or not to use https when installing the katello-ca-consumer-latest package off of the Satellite6/Capsule server | False | boolean |

The following variables are set in the role's `vars/main.yml` file.  It's recommended to not change these unless you have a good reason to:  

| Variable Name | Required | Description | Default Value | Variable Type |
| --- | :---: | :---: | --- | :---: |
| sat6_rhn_tools | no | List of Satellite5 (RHN) components to clean off of a client. | [  "rhn-setup", "rhn-client-tools", "yum-rhn-plugin", "rhnsd", "rhn-check", "rhn-lib", "spacewalk-abrt", "spacewalk-oscap", "osad", "rh-*-rhui-client", "candlepin-cert-consumer-*"] | list |
| sat6_puppet_files | no | List of files to ensure are removed from the client when `sat6_force_puppet` is set.| ["/var/lib/puppet", "/opt/puppetlabs/puppet/cache", "/opt/puppetlabs/puppet/ssl"] | list |
| sat6_sub_mgr_pkgs | no | List of packages that should be updated post client registration to ensure proper functionality of subscription-manager and its related components | ["yum", "openssl", "python", "subscription-manager", "subscription-manager-migration-*"] | list |
| sat6_katello_pkgs | no | List of packages to ensure are removed when `sat6_force_katello` is set. | ["katello-ca-consumer-*", "katello-agent", "gofer", "katello-host-tools",  "katello-host-tools-fact-plugin"] | list |

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
