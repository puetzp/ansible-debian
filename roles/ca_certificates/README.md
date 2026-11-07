ca-certificates
===============

This role configures `ca-certificates` on Debian servers. The package `ca-certificates` is installed if necessary.

Requirements
------------

None

Role Variables
--------------

| Name | Type | Description |
| --- | --- | --- |
| `ca_certificates_conf` | String | If present, the contents of this variable are copied to `/etc/ca-certificates.conf`. If absent, the current file is left unmodified. |
| `ca_certificates_custom` | Mapping | If present, each key in this mapping is used as a file name to create a file in `/usr/local/share/ca-certificates` while the value is used as file content. The role automatically adds the `.crt` extension. Unknown files are removed from the directory. If absent, the directory and its contents are left unmodified. |
| `ca_certificates_hooks` | Mapping | If present, each key in this mapping is used as a file name to create a file in `/etc/ca-certificates/update.d` while the value is used as file content. Unknown files are removed from the directory. If absent, the directory and its contents are left unmodified. |

Dependencies
------------

None

Example Playbook
----------------

```yml
- hosts: servers
  gather_facts: false
  roles:
    - puetzp.debian.ca_certificates
```

Example variables
-----------------

```yml
# group_vars/all.yml

# Uncomment to use a custom configuration file with specific certificates
# disabled. In this example the file is loaded from `files` in the playbook
# directory.
#
# Per default the configuration file from the distribution is used as-is.
#
# ca_certificates_conf: "{{ lookup('file', 'etc/ca-certificates.conf', rstrip = false) }}"

# Define custom certificates. The `.crt` extension is added automatically.
ca_certificates_custom:
  internal: |
    -----BEGIN CERTIFICATE-----
    MIIBxjCCAUygAwIBAgIURohMtoUMCXUc5l94JM0FZ/e+NjQwCgYIKoZIzj0EAwIw
    EjEQMA4GA1UEAwwHUm9vdCBDQTAeFw0yNjEwMDkyMDI4MTRaFw0zMTEwMDgyMDI4
    MTRaMBIxEDAOBgNVBAMMB1Jvb3QgQ0EwdjAQBgcqhkjOPQIBBgUrgQQAIgNiAASp
    1ACEszgYaifP6c/fVtLGhu8d9LkgmnhcPqZQb/32GsaPcr/WCCjvwSGXzKyPeUh+
    jvyX1vLv5ndv8ckp4RsG6eP46ce3g/Qt2xGOvyf2QAIUBCWf40DgmQ7BD/b+BBCj
    YzBhMB0GA1UdDgQWBBRKwzjHH1yicw3u2yVUXUPxBhRl1DAfBgNVHSMEGDAWgBRK
    wzjHH1yicw3u2yVUXUPxBhRl1DAPBgNVHRMBAf8EBTADAQH/MA4GA1UdDwEB/wQE
    AwICBDAKBggqhkjOPQQDAgNoADBlAjBp43k6PW9nqpG6LDYi8bQoweQwRl7jFyVj
    TFrGh/d/2zMx3YvNF7q8Kmyw75i++DUCMQC7JBMVDVm9uK3YfKskfQ5aAcc0X/25
    ZaG3dUDcIoBC/MOxTEhoFCo6xvaykPVOPE4=
    -----END CERTIFICATE-----

# Or use an empty map to remove all custom certificates.
#
# ca_certificates_custom: {}
```

```yml
# host_vars/ntp.internal.yml

# Use a hook script to restart chrony when the set of trusted CA
# certificates changes, i.e. after `update-ca-certificates` ran.
ca_certificates_hooks:
  restart-chrony: |
    #!/bin/sh
    systemctl restart chrony
```

Links
-----

- [update-ca-certificates(8)](https://manpages.debian.org/latest/ca-certificates/update-ca-certificates.8.en.html)
- [run-parts(8)](https://manpages.debian.org/latest/debianutils/run-parts.8.en.html)
