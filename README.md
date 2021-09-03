# Ansible Collection - artem_shestakov.conslul
## Collection roles
* **install_agent** - Install Consul on servers with roles `server` and `client` and configure a HashiCorp Consul datacenter.
* **join_agents** - Join new `client` to exists HashiCorp Consul datacenter.

## Install agent role: *install_agent*
### Variables
All variables used in this role are similar to the Consul parameters. These the detail description of parameters are available on the [Consul Configuration](https://www.consul.io/docs/agent/options) page.
* **addresses** - This is a nested object that allows setting bind addresses.
  * **dns** - The DNS server. By default, this is `client_addr`
  * **http** - The HTTP API. By default, this is `client_addr`
  * **https** - The HTTPS API. By default, this is `client_addr`
  * **grpc** - The gRPC API. Defaults is not defined
* **advertise_addr** - The advertise address is used to change the address that we advertise to other nodes in the cluster. By default, this is `client_addr`
* **advertise_addr_wan** - The advertise WAN address is used to change the address that we advertise to server nodes joining through the WAN. By default, this is `client_addr`
* **bind_addr** - The address that should be bound to for internal cluster communications. By default, this is `client_addr`
* **bootstrap_expect** - This flag provides the number of expected servers in the datacenter. By default, this is number if servers group of inventory, but less then 5.
* **client_addr** - The address to which Consul will bind client interfaces, including the HTTP and DNS servers. By default, this is default host's IPv4 address. (`ansible_default_ipv4.address`)
* **data_dir** - This flag provides a data directory for the agent to store state. By default, this is `/opt/consul`
* **datacenter** - This flag controls the datacenter in which the agent is running. By default, this is `dc1`
* **disable_update_check** - Disables automatic checking for security bulletins and new version releases. By default, this is `false`
* **domain** - Consul responds to DNS queries in the "consul." domain. This flag can be used to change that domain. By default, this is not defined
* **enable_script_checks** - This controls whether health checks that execute scripts are enabled on this agent. By default, this is `false`
* **enable_syslog** - This flag enables logging to syslog. By default, this is `true`
* **encrypt** - Specifies the secret key to use for encryption of Consul network traffic. By default, will be generated automatically
* **log_level** - The level of logging to show after the Consul agent has started. By default, this is `INFO`
* **node_name** - The name of this node in the cluster. By default, this is hostname (`ansible_hostname`)
* **performance** - nested object that allows tuning the performance of different subsystems in Consul.
  * **leave_drain_time** - A duration that a server will dwell during a graceful leave in order to allow requests to be retried against other Consul servers. By default, this is `5s`
  * **raft_multiplier** - An integer multiplier used by Consul servers to scale key Raft timing parameters. By default, this is `1`
  * **rpc_hold_timeout** - A duration that a client or server will retry internal RPC requests during leader elections. By default, this is `7s`
* **ports** - This is a nested object that allows setting the bind ports for the following keys:
  * **dns** - The DNS server, -1 to disable. Default `8600`
  * **http** - The HTTP API, -1 to disable. Default `8500`.
  * **https** - The HTTPS API, -1 to disable. Default -`1` (disabled)
  * **grpc** - The gRPC API, -1 to disable. Default -`1` (disabled)
  * **serf_lan** - The Serf LAN port. Default `8301`
  * **serf_wan** - The Serf WAN port. Default `8302`
  * **server** - Server RPC address. Default `8300`
  * **sidecar_min_port** - Inclusive minimum port number to use for automatically assigned sidecar service registrations. Default `21000`
  * **sidecar_max_port** - Inclusive maximum port number to use for automatically assigned sidecar service registrations. Default `21255`
  * **expose_min_port** - Inclusive minimum port number to use for automatically assigned exposed check listeners. Default `21500`
  * **expose_max_port** - Inclusive maximum port number to use for automatically assigned exposed check listeners. Default `21755`
  ```yaml
  ports:
    dns: "{{ ports_dns | default(8600, true) }}"
    http: "{{ ports_http | default(8500, true) }}"
    https: "{{ ports_https | default(-1, true) }}"
    grpc: "{{ ports_grpc | default(-1, true) }}"
    serf_lan: "{{ ports_serf_lan | default(8301, true) }}"
    serf_wan: "{{ ports_serf_wan | default(8302, true) }}"
    server: "{{ ports_server | default(8300, true) }}"
    sidecar_min_port: "{{ ports_sidecar_min_port | default(21000, true) }}"
    sidecar_max_port: "{{ ports_sidecar_max_port | default(21255, true) }}"
    expose_min_port: "{{ ports_expose_min_port | default(21500, true) }}"
    expose_max_port: "{{ ports_expose_max_port | default(21755, true) }}"
  ```
* **primary_datacenter** - This designates the datacenter which is authoritative for ACL information, intentions and is the root Certificate Authority for Connect. It must be provided to enable ACLs. All servers and datacenters must agree on the primary datacenter.
* **raft_protocol** - This controls the internal version of the Raft consensus protocol used for server communications. By default, this is `3`
* **recursors** - Specifies the address of an upstream DNS server. By default, this is not defined
* **retry_interval** - Time to wait between join attempts. Defaults to 30s.
* **retry_join** - Addresses of another agents to join upon starting up. Allows retrying a join until it is successful. By default, this is not defined
* **retry_join_wan** - List of addresses to attempt joining to WAN. By default, this is not defined
* **ui** - Enables the built-in web UI server and the required HTTP routes. By default, this is `true`


 Below is playbook sample with that variables:
```yaml
---
- hosts: all
  remote_user: consul_user
  become: yes
  roles:
    - "install_agent"
  vars:
    ports:
      dns: 53
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