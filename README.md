Ansible Role: Prometheus SQL Exporter ([lrk.prometheus-sql-exporter](https://galaxy.ansible.com/lrk/prometheus-sql-exporter/))
=========
[![Build Status](https://travis-ci.org/lrk/ansible-role-prometheus-sql-exporter.svg?branch=master)](https://travis-ci.org/lrk/ansible-role-prometheus-sql-exporter)

An Ansible Role that install [Prometheus](https://prometheus.io) [SQL Exporter](https://github.com/free/sql_exporter).


Supported OSes
--------------
- Centos 7

Requirements
------------

None

Role Variables
--------------

Available variables along with default values are listed below (see `defaults/main.yml`)
```yml
---
# defaults file for ansible-prometheus-sql-exporter

prom_sexp_group: prometheus_sql_exporter
prom_sexp_user: prometheus_sql_exporter
# Prometheus SQL Exporter release version (see: https://github.com/free/sql_exporter/releases for available versions)
prom_sexp_version: 0.4

# Prometheus SQL Exporter installation path
prom_sexp_path_install: /opt/prometheus/sql-exporter

# Prometheus SQL Exporter configuration path
prom_sexp_path_config: /etc/prometheus/sql-exporter

# Prometheus SQL Exporter collectors path
prom_sexp_path_collectors: "{{ prom_sexp_path_config }}/collectors"
prom_sexp_path_collectors_suffix: ".collector.yml"
# Prometheus SQL Exporter logs path
prom_sexp_path_logs: "/var/log/prometheus/sql-exporter"


# Prometheus SQL Exporter service configuration
# Service name prefix, the suffix is taken from target_name (see below)
prom_sexp_service_name_prefix: prom-sql-exp
# log to standard error as well as files (default true)
prom_sexp_alsologtostderr: true

# Array of Prometheus SQL Exporter targets
# Each target:
#   - is converted to a configuration file and multiple collector files
#   - get its own systemd unit file
#   - YOU MUST ensure that listen_address DOES NOT overlap beteen targets to avoid conflicts
#
# Each collector file, which is based on the collector name, MUST BE UNIQUE
# See example below
prom_sexp_targets: []

# prom_sexp_targets:
#     # the target name, configuration file generated is based on this value
#   - name: target_name
#     # if the value==present, configuration files are created, if value!=present, configuration files are deleted. Default value: present
#     state: present
#     # Address to listen on for web interface and telemetry. (default ":9399")
#     listen_address: ":9399"
#     # Path under which to expose metrics. (default "/metrics")
#     metrics_path: "/metrics"
#     # Scrape timeouts ensure that:
#     #   (i)  scraping completes in reasonable time and
#     #   (ii) slow queries are canceled early when the database is already under heavy load
#     # Prometheus informs targets of its own scrape timeout (via the "X-Prometheus-Scrape-Timeout-Seconds" request header)
#     # so the actual timeout is computed as:
#     #   min(scrape_timeout, X-Prometheus-Scrape-Timeout-Seconds - scrape_timeout_offset)
#     #
#     # If scrape_timeout <= 0, no timeout is set unless Prometheus provides one. The default is 10s.
#     scrape_timeout: 10s
#     # Subtracted from Prometheus' scrape_timeout to give us some headroom and prevent Prometheus from timing out first.
#     #
#     # Must be strictly positive. The default is 500ms.
#     scrape_timeout_offset: 500ms
#     # Minimum interval between collector runs: by default (0s) collectors are executed on every scrape.
#     min_interval: 0s
#     # Maximum number of open connections to any one target. Metric queries will run concurrently on multiple connections,
#     # as will concurrent scrapes.
#     #
#     # If max_connections <= 0, then there is no limit on the number of open connections. The default is 3.
#     max_connections: 3
#     # Maximum number of idle connections to any one target. Unless you use very long collection intervals, this should
#     # always be the same as max_connections.
#     #
#     # If max_idle_connections <= 0, no idle connections are retained. The default is 3.
#     max_idle_connections: 3
#     # Data source name always has a URI schema that matches the driver name. In some cases (e.g. MySQL)
#     # the schema gets dropped or replaced to match the driver expected DSN format.
#     data_source_name: 'sqlserver://prom_user:prom_password@dbserver1.example.com:1433'
#     # Collectors (referenced by name) to execute on the target.
#     collector_names: [my_collector_name]
#     # Collector files specifies a list of globs. One collector definition per file.
#     collector_files_globs:
#       - "{{ prom_sexp_path_collectors }}/*{{ prom_sexp_path_collectors_suffix }}"


# A collector is a named set of related metrics that are collected together. It can be referenced by name, possibly
# along with other collectors.
#
# Collectors may be defined inline (under `collectors`) or loaded from `collector_files` (one collector per file).

prom_sexp_collectors: []

# prom_sexp_collectors:
#   - name: collector name  # The collector name
#     # if the value==present, configuration files are created, if value!=present, configuration files are deleted. Default value: present
#     state: present
#     min_interval: 0s      # Similar to global.min_interval, but applies to this collector only.
#     # Array of metrics to be added to this collector
#     # See example below
#     metrics: {{ my_collector_metrics }}
#     # Named queries, referenced by one or more metrics, through query_ref.
#     # See example below
#     queries: {{ my_collector_queries }}
#
# # A metric is a Prometheus metric with name, type, help text and (optional) additional labels, paired with exactly
# # one query to populate the metric labels and values from.
# #
# # The result columns conceptually fall into two categories:
# #  * zero or more key columns: their values will be directly mapped to labels of the same name;
# #  * one or more value columns:
# #     * if exactly one value column, the column name name is ignored and its value becomes the metric value
# #     * with multiple value columns, a `value_label` must be defined; the column name will populate this label and
# #       the column value will popilate the metric value.
# my_collector_metrics:
#   - name: my metric name                    # The metric name
#     type: counter                           # The Prometheus metric type
#     help: 'help for / description of my metric'
#     key_labels: []                          # Optional set of labels derived from key columns.
#     value_label: valuelabel                 # Required when multiple value columns are configured.
#     # Multiple value columns: their name is recorded in the label defined by `attrubute_label`
#     metric_values:
#       - val1
#       - val2
#     query: "SELECT columns FROM table ...;" # optionnal, but either query or query_ref is required
#     query_ref: "name of one of my queries" # optionnal, but either query or query_ref is required
#
# # List of all queries going to the 'queries:' section of the collector configuration
# my_queries:
#   - name: "query name"                    # name of the query, *mandatory*
#     query: |                              # use '|' (pipe character) to use multiline, *mandatory*
#       SELECT columns FROM table...

```

Dependencies
------------

None

Example Playbook
----------------

```
    - hosts: servers
      roles:
         - lrk.prometheus-sql-exporter
```

 License
 -------

 Apache License Version 2.0

 References
 ----------

- [Prometheus.io](https://prometheus.io)
- [Prometheus SQL Exporter](https://github.com/free/sql_exporter)

Author Information
------------------
This role was created by [Lrk](https://github.com/lrk).
