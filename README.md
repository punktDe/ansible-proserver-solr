# ansible-proserver-solr

An Ansible role that sets up Apache Solr on a Proserver

Downloads the defined Solr version from [archive.apache.org](https://archive.apache.org) and enables the service.

## Dependencies
* [ansible-proserver-oauth2-proxy](https://github.com/punktDe/ansible-proserver-oauth2-proxy)
* [ansible-proserver-nginx](https://github.com/punktDe/ansible-proserver-nginx) or [ansible-proserver-apache](https://github.com/punktDe/ansible-proserver-apache)
* [ansible-proserver-dehydrated](https://github.com/punktDe/ansible-proserver-dehydrated)
* [ansible-proserver-supervisord](https://github.com/punktDe/ansible-proserver-supervisord)

## Background

This role deactivates the built-in FreeBSD rc.d Solr service and does not interfere with its working directories (/var/solr, /var/solr/db, etc.).

Instead, it uses [supervisord](https://github.com/punktDe/ansible-proserver-supervisord) for service management, and works with the following folders:

|Directory|Description|
|---|---|
|/var/opt/solr/solr|Solr working directory|
|/var/opt/solr/home|Solr home|
|/var/log/solr-ansible|Solr logs|

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

**Use with caution** – this will also remove all your cores and custom configurations for them.

```yaml
solr:
  overwrite_home: yes
```

If disabled, it will still handle the `solr.xml` as followed:

In case `solr.version` is **older than 9.x**, it will comment out the following parameters in your `solr.xml`:

* allowUrls
* modules
* zkCredentialsInjector
* distributedClusterStateUpdates
* distributedCollectionConfigSetExecution
* minStateByteLenForCompression
* stateCompressor
* hideStackTrace

These configuration parameters are present in the default `solr.xml` file shipped with 9.x, but are incompatible with versions 8.x and older.

The commented out lines are marked with the text `disabled by Ansible`.

In case `solr.version` is 9.x or newer, the role will uncomment all config lines that contain the text `disabled by Ansible`.

### solr.prefix
The directories for Solr
```yaml
solr:
  prefix:
    bin: /var/opt/solr/solr
    home: /var/opt/solr/home
    logs: /var/log/solr-ansible
    ext_solr: /var/opt/solr/ext-solr
```

### solr.version
Solr version to install. Make sure the version exists on either https://archive.apache.org/dist/lucene/solr/ (pre 9.x) or https://archive.apache.org/dist/solr/solr/ (9.x)
```yaml
solr:
  version: 9.7.0
```

### solr.ext_solr
**Default**: disabled

Prepares Solr for the TYPO3 extension [EXT:solr](https://github.com/TYPO3-Solr/ext-solr) and creates its cores. Only for Solr servers used by TYPO3, so it is off unless `enabled` is set.

`version` is the EXT:solr release whose Solr server files are deployed. Use the version the TYPO3 project has installed (`composer show apache-solr-for-typo3/solr`), so the configset matches the extension. The role downloads that release from GitHub and installs the following from its `Resources/Private/Solr` folder into the Solr home:

* `solr.xml`, which loads the TYPO3 plugin jars from `typo3lib/`
* `typo3lib/` with the plugin jars
* the `ext_solr_*` configset

`cores` maps each core name to the language of its schema, i.e. a folder in the configset's `conf/` (`german`, `english`, ...). Every core gets `cores/<name>/core.properties` and stores its index in `data/<name>`, both inside the Solr home. Solr loads new or changed cores when the role restarts it. Cores are never deleted; setting a core to `null` only stops the role from managing it.

The synonyms and stopwords maintained in the TYPO3 backend are stored by Solr as `_schema_analysis_*.json` inside the configset. The role adds missing ones but never overwrites existing ones.

```yaml
solr:
  ext_solr:
    enabled: yes
    version: 14.0.2
    cores:
      core_de: german
      core_en: english
```
