# ahuffman.satellite6_bootstrap

To provide a method to painlessly migrate Satellite 5 clients to Satellite 6 by making use of Ansible.  This role also provides for the initial registration of clients to Satellite6 or for re-registration of clients from an existing Satellite6 server.  The role ensures all RHN (Satellite5) specific components are removed as part of the process.

***Please see the `Dependencies` Section of this documentation to ensure you meet all requirements***

***Please note that when calling this Role, you should have collected facts first***

## Role Variables
The following variables are set in the role's `defaults/main.yml` file:  

| Variable Name | Required | Description | Default Value | Variable Type |
| --- | :---: | :---: | --- | :---: |
| sat6_server | yes | FQDN of the "master" Satellite6 server (i.e. where content synchronizes to from Red Hat CDN). This cannot be a Capsule server, as we require access to the Satellite6 API.| "" | string |
| sat6_capsule | no | If you require your clients to register to a Capsule server, this will be the FQDN of the Satellite6 capsule server to register to. If not provided, this will default to `sat6_server` when not provided (i.e. clients will register to the `sat6_server`).| N/A | string |
| sat6_user | yes | Username of a Satellite 6 user that has permissions to create hosts, usually an admin user. | "" | string |
| sat6_pass | yes | Password of the `sat6_user` that has permissions to create hosts, usually an admin user. | "" | string |
| sat6_organization | yes | Organization to register the host in on the Satellite6 server.  This should be the Organization label found in Satellite 6.| "" | string |
| sat6_activation_key | yes | Activation key to use when registering the host with Satellite6. | "" | string |
| sat6_location | no | Name of the location to register the host with in Satellite6.| "" | string |
| sat6_enable_repos | no | List of repositories to enable after Satellite6.  registration is complete. | [] | list |
| sat6_remote_execution | no | Place a file named `[sat6_server]-id_rsa_foreman_proxy.pub` in this role's `files` directory.  You can retrieve this file from your Satellite6 server. Please note that the filename prefix should match your Satellite6 or Capsule server name set with the `sat6_server` or `sat6_capsule` variable| False | boolean |
| sat6_remote_execution_user | no | User account to install the Satellite6 remote execution key for. | "root" | string |
| sat6_remote_execution_auth_key_path | no | Path to your authorized keys file if it is not `/home/<user>/.ssh/authorized_keys`. | "" | string |
| sat6_puppet | no | Whether or not to perform Puppet installation and configuration. | False | boolean |
| sat6_puppet_environment | no | Puppet environment to configure the client's puppet agent with. | "production" | string |
| sat6_force_puppet | no | Whether or not to force an uninstall and cleanup of a client's puppet agent and configuration. | False | boolean |
| sat6_update_client | no | Whether or not to fully update (patch) the client being registered as part of the process. | False | boolean |
| sat6_force_katello | no | Whether or not to force the uninstall and cleanup of the client's katello agent components. Set this to `True` to reregister a host.| False | boolean |
| sat6_https | no | Whether or not to use https when installing the katello-ca-consumer-latest package off of the Satellite6/Capsule server | False | boolean |
| sat6_legacy_purge | no | Whether or not to remove the host entry from Satellite 5 during migration.  This will be ignored if `/etc/sysconfig/rhn/systemid` does not exist at time of execution. | False | boolean |
| sat6_legacy_user | no | Username of the Satellite 5 user that has permissions to remove a host entry.  Required if `sat6_legacy_purge` is set to `True`.| "" | string |
| sat6_legacy_pass | no | Password of the Satellite 5 user that has access to remove a host entry.  Required if `sat6_legacy_purge` is set to `True`. | "" | string |
| sat6_rhsm_port | no | Force a non-default Satellite 6 subscription-manager port.  See /etc/rhsm/rhsm.conf, server section, port key. | "443" | string |
| sat6_os_release | no | Configure which operating system release to use. | "" | string |
| sat6_host_group | yes | Title of the Host Group to associate with the host | "" | string |
| sat6_update_existing | no | Whether or not to update an existing Satellite 6 host (i.e. host exists in Satellite 6 already but want to update something like activation key, host group, or location) to specified parameters. | False | boolean |

The following variables are set in the role's `vars/main.yml` file.  It's recommended to not change these unless you have a good reason to:  

| Variable Name | Required | Description | Default Value | Variable Type |
| --- | :---: | :---: | --- | :---: |
| sat6_rhn_tools | no | List of Satellite5 (RHN) components to clean off of a client. | [  "rhn-setup", "rhn-client-tools", "yum-rhn-plugin", "rhnsd", "rhn-check", "rhn-lib", "spacewalk-abrt", "spacewalk-oscap", "osad", "rh-*-rhui-client", "candlepin-cert-consumer-*"] | list |
| sat6_puppet_files | no | List of files to ensure are removed from the client when `sat6_force_puppet` is set.| ["/var/lib/puppet", "/opt/puppetlabs/puppet/cache", "/opt/puppetlabs/puppet/ssl"] | list |
| sat6_sub_mgr_pkgs | no | List of packages that should be updated post client registration to ensure proper functionality of subscription-manager and its related components | ["yum", "openssl", "python", "subscription-manager", "subscription-manager-migration-*"] | list |
| sat6_katello_pkgs | no | List of packages to ensure are removed when `sat6_force_katello` is set. | ["katello-ca-consumer-*", "katello-agent", "gofer", "katello-host-tools",  "katello-host-tools-fact-plugin"] | list |

## Dependencies
As of release 1.0.3 this Role requires the [ahuffman.sat6_create_hosts](https://galaxy.ansible.com/ahuffman/sat6_create_hosts) Role.  This dependency is due to adding the feature to be able to specify a host location (`sat6_location`) and Host Group (`sat6_host_group`) during migration.

To install the required Role, `ansible-galaxy install ahuffman.sat6_create_hosts`.  

With Ansible Tower/AWX this dependency could be specified in `requirements.yml` to avoid pre-installation.

Please note that `ahuffman.sat6_create_hosts` depends on `ahuffman.api` and will be auto-installed with the above `ansible-galaxy` command.


## Example Playbook
```yaml
---
- hosts: "servers"
  vars_files:
    - "vars/secrets.yml"
  roles:
    - role: "ahuffman.satellite6_bootstrap"
      sat6_server: "mysat6.foo.bar"
      sat6_user: "mysat6user"
      sat6_pass: "{{ sat6_user_pass_from_vault }}"
      sat6_legacy_purge: True
      sat6_legacy_user: "mysat5user"
      sat6_legacy_pass: "{{ sat5_legacy_user_pass_from_vault }}"
      sat6_activation_key: "mykey1"
      sat6_organization: "myorg1"
      sat6_update_existing: True #force existing sat6 host to be updated to what we've specified
      sat6_update_client: True #patch the system post registration
      sat6_enable_repos:
        - "rhel-7-server-optional-rpms"
      sat6_remote_execution: True
      sat6_puppet: True
      sat6_host_group: "RHEL7/RHEL7-Virtual-Prod"
      sat6_location: "Raleigh"
```
## License
[MIT](LICENSE)

## Author
[Andrew J. Huffman](https://github.com/ahuffman)
