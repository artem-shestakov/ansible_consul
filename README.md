# Ansible Collection - artem_shestakov.conslul
## Collection roles
* `install_agent`  
Install Consul on servers with roles `server` and `client` and configure a HashiCorp Consul datacenter.
* `join_agents`  
Join new `client` to exists HashiCorp Consul datacenter.

## Install agent role
### Variables
All variables used in this role are similar to the Consul parameters. These the detail description of parameters are available on the [Consul Configuration](https://www.consul.io/docs/agent/options) page. Below is playbook sample with that variables:
```yaml
---
- hosts: all
  remote_user: consul_user
  become: yes
  roles:
    - "install_agent"
  vars:
    ports:
      dns: 8600
      http: 8500
      https: -1
    log_level: INFO
    node_name: "{{ ansible_hostname }}"
    enable_syslog: true
    verify_incoming: true
    verify_outgoing: true
    verify_server_hostname: true
```
### Gossip encrypt key
New encrypt key will be generated(by `consul keygen` command) if there is now `encrypt` parameter in `consul.hcl` file or file not exists on first consul server.
If `encrypt` parameter exists and it's not empty string, role will use it for all instances.