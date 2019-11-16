Metricbeat Role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-metricbeat/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-metricbeat.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-metricbeat)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-metricbeat/badges/master/pipeline.svg)](https://gitlab.com/lean-delivery/ansible-role-metricbeat/pipelines)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.metricbeat-blue.svg)](https://galaxy.ansible.com/lean_delivery/metricbeat)
![Ansible](https://img.shields.io/ansible/role/d/role_id.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2Frole_id%2F&query=$.min_ansible_version)


## Summary


This role:
  - installs metricbeat on Ubuntu, Debian, CentOS, Windows
  - copies prepared configuration file (log path, connect to elasticsearch, etc.)




Role tasks
------------


- Prepare server (add elastic repo)
- [Optional] Create folder(s) for custom paths
- Install metricbeat
- Copy configuration file

Requirements
------------

- Minimal Version of the ansible for installation: 2.5
 - **Supported OS**:
   - CentOS
     - 7, 8
   - Ubuntu
     - 16.04
   - Debian
     - 8, 9
   - Windows Server
     - 2016, 2019

Role Variables
--------------

You can override any variable below by setting "variable: value" in playbook.


- `metricbeat_version`
Is used to select main Metricbeat branch to be installed (5.x or 6.x current stable versions). Default value is `6`.
- `metricbeat_last_version`
Is used to select specific Metricbeat version to be installed. Default value is `6.6.0`
- `elastic_gpg_key`
GPG-key from elasticsearch repository. Default value is `https://artifacts.elastic.co/GPG-KEY-elasticsearch`
- `metricbeat_node_name`
Name of the metricbeat node. Default value is `{{ inventory_hostname }}`. If this options is not defined, the hostname is used.
- `metricbeat_ssl_enabled`
Turns on/off SSL connection between metricbeat and logstash/elasticsearch. SSL options should be set by corresponding dict fields like shown below:
```
  ssl:
    key: "/etc/pki/tls/private/server.key"
    certificate: "/etc/pki/tls/certs/server.crt"
    certificate_authorities: "/etc/pki/CA/ca-root.pem"
```
 in case of Windows setup:   
 ```
  ssl:
    key: 'c:\tls\private\server.key'
    certificate: 'c:\tls\certs\server.pem'
    certificate_authorities: 'c:\CA\ca-root.pem'
```


The `path` section of the configuration options defines where Metricbeat looks for its files. For example, Metricbeat looks for the Elasticsearch template file in the configuration path and writes log files in the logs path. Metricbeat looks for its registry files in the data path. Default values for Linux host are set up this way:
```
path:
  home: /usr/share/metricbeat
  config: /etc/metricbeat
  data: /var/lib/metricbeat
  logs: /var/log/metricbeat
```
in case of Windows setup default paths look like:
```
path:
  home: 'c:\program files\metricbeat'
  config: 'c:\program files\metricbeat'
  data: 'c:\programdata\metricbeat'
  logs: 'c:\programdata\metricbeat\logs'
```
- `win_download_path`
Temp directory for Windows to download and upzip Metricbeat package. Default value is `'{{ ansible_env.TEMP }}/metricbeat'` (ansible_env.TEMP value solves idempotence issue)


- `input_logpath` 
Path to log files. 

Default value for *NIX OS family is `"/var/log/*.log"`

Default value for WIN OS family is `'c:\windows\*.log'`


## Output customization:
- `metricbeat_output`
Is used to configure what output to use when sending data (`elasticsearch` or `logstash`). Default value is `elasticsearch`


- `elasticsearch.host`
Array of hosts to connect to. Default value is `localhost`
- `elasticsearch.port`
Value for setting custom port. Default value is `9200`


- `logstash.host`
Array of hosts to connect to. Default value is `localhost`
- `logstash.port`
Value for setting custom port. Default value is `5044`


## Advanced config parameters:


The `metricbeat(systemd)\initd` section of the configuration  options defines which init script will be used to manage metricbeat service depending on the *nix OS. Custom paths will be taken into account (if configured).
- `metricbeat_service_name`
Name of nssm\init script, which manages metricbeat service


- `metricbeat_bulk_max_size`
Maximum number of events to bulk in a single Logstash request. Default value is `500`
- `metricbeat_worker`
Number of workers per Elasticsearch host. Default value is `1`
- `metricbeat_logging_to_syslog`
Send all logging output to syslog. Default value is `false`
- `metricbeat_logging_to_files`
Send all logging output to rotating files. Default value is `true`
- `metricbeat_rotateeverybytes`
Defines log file size limit. Defalt value is `104857600` = `100MB`
- `metricbeat_keepfiles`
Number of log files to keep. Default value is `30`
- `metricbeat_ignore_older`
Value (any time strings like 2h, 5m can be used) above which files will be ignored. Default value is `0` (disabled)
- `metricbeat_scan_frequency`
Defines how often metricbeat checks file updates. Default value is `15s`
- `metricbeat_harvester_buffer_size`
Defines the buffer size. Default value is `65535`
- `metricbeat_logname`
Name of the logging files. Default value is `"metricbeat.log"`


## Dependencies
------------


ca-cert (only for installation with SSL)


Example Playbook
----------------


### Installing Metricbeat 6.x version:


```yaml
- name: Install metricbeat
  hosts: all
  roles:
    - role: ansible-role-metricbeat
```
### Installing Metricbeat 6.x version with custom elasticsearch output:


```yaml
- name: Install metricbeat
  hosts: all
  roles:
    - role: ansible-role-metricbeat
  vars:
    elasticsearch:
      host: elasticsearch.example.com
      port: 9200
```


License
-------
Apache


Author Information
------------------


authors:
  - Lean Delivery Team <team@lean-delivery.com>
