# Ansible Collection - artem_shestakov.conslul
## Collection roles
* `install_agent`  
Install Consul on servers with roles `server` and `client` and configure a HashiCorp Consul datacenter.
* `join_agents`  
Join new `client` to exists HashiCorp Consul datacenter.

## Install agent role
### Variables
All variables used in this role are similar to the Consul parameters. These parameters are available in the [Configuration](https://www.consul.io/docs/agent/options) section.