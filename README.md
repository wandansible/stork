Ansible role: Stork
===================

Install and configure Stork for Kea DHCP server.

Role Variables
--------------

```
ENTRY POINT: main - Install and configure Stork for Kea DHCP server

OPTIONS (= is mandatory):

- postgresql_user
        Name of the postgresql unix user
        [Default: postgres]
        type: str

- stork_agent_config
        Extra agent configuration, see
        https://stork.readthedocs.io/en/latest/install.html#agent-
        configuration-settings
        [Default: (null)]
        type: dict

- stork_agent_credentials
        Agent authentication credentials with a kea control agent, see
        the basic auth example given here:
        https://stork.readthedocs.io/en/latest/install.html#securing-
        connections-between-stork-agent-and-the-kea-control-agent
        [Default: (null)]
        type: dict

- stork_agent_host
        Agent listen address. In most cases, this should be set to the
        hostname of the host as the Common Name in the generated cert
        will be set to the value of stork_agent_host, which is
        verified by the stork server. By default, this is set to
        inventory_hostname.
        [Default: (null)]
        type: str

- stork_agent_install
        If true, install and configure stork agent
        [Default: True]
        type: bool

- stork_agent_port
        Agent listen port
        [Default: 8002]
        type: int

- stork_agent_server_url
        URL to stork server for the agent to register to
        [Default: http://localhost:8001]
        type: str

- stork_apt_key_fingerprint
        Fingerprint for the stork apt key
        [Default: (null)]
        type: str

- stork_bind9_exporter_address
        Bind9 exporter listen address
        [Default: localhost]
        type: str

- stork_bind9_exporter_port
        Bind9 exporter listen port
        [Default: 9119]
        type: int

- stork_database
        Name for the stork postgresql database
        [Default: stork]
        type: str

- stork_database_password
        Password for the stork postgresql database user, required when
        stork_server_install = true
        [Default: (null)]
        type: str

- stork_database_user
        Name for the stork postgresql database user
        [Default: stork]
        type: str

- stork_kea_exporter_address
        Kea exporter listen address
        [Default: localhost]
        type: str

- stork_kea_exporter_port
        Kea exporter listen port
        [Default: 9547]
        type: int

- stork_server_address
        Server listen address
        [Default: localhost]
        type: str

- stork_server_config
        Extra server configuration, see
        https://stork.readthedocs.io/en/latest/install.html#setup
        [Default: (null)]
        type: dict

- stork_server_install
        If true, install and configure stork server
        [Default: True]
        type: bool

- stork_server_port
        Server listen port
        [Default: 8001]
        type: int
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/stork,main,wandansible.stork
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.stork
      src: https://github.com/wandansible/stork

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: dhcp_servers
      roles:
         - role: wandansible.stork
           become: true
           vars:
             kea_control_agent_config:
               http-host: 127.0.0.1
               http-port: 8000
               cert-required: false
               authentication:
                 type: basic
                 realm: kea-control-agent
                 clients:
                   - user: stork
                     password: CHANGEME
               control-sockets:
                 dhcp4:
                   comment: kea dhcp4 server
                   socket-type: unix
                   socket-name: /run/kea/kea-dhcp4.sock
               loggers:
                 - name: kea-ctrl-agent
                   output_options:
                     - output: /var/log/kea/kea-ctrl-agent.log
                   severity: WARN

            stork_database_password: CHANGEME
            stork_agent_credentials:
              basic_auth:
                - ip: 127.0.0.1
                  port: 8000
                  user: stork
                  password: CHANGEME
