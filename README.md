# ansible-proserver-solr

Downloads a defined version of apache solr and tika from archive.apache.org and enables the services. 

Define the solr and tika version that fits to your project using the paths

```yaml
solr:
  version: 9.2.0
  tika:
    version: 2.8.0
```

on your host_vars or group_vars file.
