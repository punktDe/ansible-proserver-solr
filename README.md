# ansible-proserver-solr

An Ansible role that sets up Apache Solr and Apache Tika on a Proserver

Downloads the defined versions of Solr and Tika from [archive.apache.org](https://archive.apache.org) and enables their respective services.

### Example configuration

The default configuration values can be found in defaults/main.yaml

```yaml
solr:
  domain: https://solr.example.com
  prefix:
    bin: /var/solr
    var: /var/db/solr
  version: 9.2.0
  tika:
    prefix:
      bin: /var/opt/tika
    version: 2.8.0
  oauth2_proxy: main # https://github.com/punktDe/ansible-proserver-oauth2-proxy
  # Will erase the Solr home and replace it with the example 
  # home folder shipped with the respective Solr version
  overwrite_home: no 
```
