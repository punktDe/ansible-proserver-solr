# ansible-proserver-solr

An Ansible role that sets up Apache Solr and Apache Tika on a Proserver

Downloads the defined versions of Solr and Tika from [archive.apache.org](https://archive.apache.org) and enables their respective services.

## Dependencies
* [ansible-proserver-oauth2-proxy](https://github.com/punktDe/ansible-proserver-oauth2-proxy)
* [ansible-proserver-nginx](https://github.com/punktDe/ansible-proserver-nginx) or [ansible-proserver-apache](https://github.com/punktDe/ansible-proserver-apache)
* [ansible-proserver-dehydrated](https://github.com/punktDe/ansible-proserver-dehydrated)
* [ansible-proserver-supervisord](https://github.com/punktDe/ansible-proserver-supervisord)

## Background

This role deactivates the built-in FreeBSD rc.d services for Solr and Tika and does not interfere with their respective working directories (/var/solr, /var/solr/db, etc.).

Instead, it uses [supervisord](https://github.com/punktDe/ansible-proserver-supervisord) for service management, and works with the following folders:

|Directory|Description|
|---|---|
|/var/opt/solr/solr|Solr working directory|
|/var/opt/solr/home|Solr home|
|/var/opt/solr/tika|Tika working directory|
|/var/log/solr-ansible|Solr and Tika Logs|

## Configuration

The default configuration values can be found in defaults/main.yaml

### solr.url
**Default**: empty

The URL for the Solr WebUI
```yaml
solr:
  url: https://solr.example.com
```

### solr.oauth2_proxy
**Default**: empty

The oauth2_proxy configuration that should be used for Solr WebUI authentiacation.

Should be defined under `oauth2_proxy.$configuration` e.g. `oauth2_proxy.main` or `oauth2_proxy.solr`

If empty, the WebUI will be publicly available, with no authentication.
```yaml
solr:
  oauth2_proxy: main
```

### solr.overwrite_home
**Default**: no

If enabled, will erase the Solr home directory (default: /var/opt/solr/home) on version change.

Use with caution – this will also remove all your cores and custom configurations for them.

```yaml
solr:
  overwrite_home: yes
```

### solr.prefix
The directories for Solr and Tika
```yaml
solr:
  prefix:
    bin: /var/opt/solr/solr
    home: /var/opt/solr/home
    tika: /var/opt/solr/tika
    logs: /var/log/solr-ansible
```

### solr.version
Solr version to install. Make sure the version exists on either https://archive.apache.org/dist/lucene/solr/ (pre 9.x) or https://archive.apache.org/dist/solr/solr/ (9.x)
```yaml
solr:
  version: 9.7.0
```

### solr.tika.version
Tika version to install. Make sure the version exists on https://archive.apache.org/dist/tika
```yaml
solr:
  tika:
    version: 3.0.0
```
