---
title: Configuring LDAP authentication for InfluxDB Enterprise
description: Configuring LDAP authentication for InfluxDB Enterprise, including steps for meta nodes and data nodes, and testing LDAP connectivity.
menu:
  enterprise_influxdb_1_6:
    name: Configuring LDAP authentication
    weight: 40
    parent: Administration
---
InfluxDB Enterprise can be configured to query a Lightweight Directory Access Protocol(LDAP)-compatible directory service to determine user permissions and to synchronize a directory service into InfluxDB so that the remote directory service does not need to be queried for each request.

## Requirements

To use LDAP with InfluxDB Enterprise, you need to support the following requirements:

* All users are managed in the remote LDAP service.


## Configuring the InfluxDB Enterprise configuration file

To use LDAP with InfluxDB Enterprise, make the following changes to the InfluxDD Enterprise configuration:

* Provide an HTTP Basic Authentication header. See [Authentication and authorization in InfluxDB](/influxdb/v1.6/administration/authentication_and_authorization/) for details on using HTTP Basic Authentication with InfluxDB.
* Provide a username and password using the following HTTP query parameters
  - `u`: username
  - `p`: password
* Enable HTTP authentication.
  - Set the environment variable `INFLUXDB_HTTP_AUTH_ENABLED`, or the corresponding `[http]` setting `auth-enabled`, to `true`. Default is `false`.
* Configure the HTTP shared secret to validate requests using JSON web tokens (JWT) and sign each HTTP payload with the secret and username.
  - Set `INFLUXDB_HTTP_SHARED_SECRET`, or the corresponding `[http]` setting `shared-secret`, to `"<shared_secret>""` (your shared secret value). Default value is `""`.


## Configuring the InfluxDB meta nodes

Typically, database operators, and not database clients, interact directly with the meta nodes.

To use LDAP with InfluxDB Enterprise, you must:

* Provide an HTTP Basic Authentication header. See [Authentication and authorization in InfluxDB](/influxdb/v1.6/administration/authentication_and_authorization/) for details on using HTTP Basic Authentication with InfluxDB.
* Provide a username and password as HTTP query parameters
  - `u`: username
  - `p`: password
* Configure the meta node META shared secret to validate requests using JSON web tokens (JWT) and sign each HTTP payloads with the secret and username.
      - Set the environment variable `INFLUXDB_META_SHARED_SECRET`, or `meta` configuration setting `meta-shared-secret` to `"<shared-secret>"`.
* If authentication is enabled on meta nodes, then the data nodes must be configured for:
    - `INFLUXDB_META_META_AUTH_ENABLED` environment variable, or `[http]` configuration setting `meta-auth-enabled`, is set to `true`. Default value is `false`. This value must be the same value as the meta node's `meta.auth-enabled` configuration.
      - `INFLUXDB_META_META_INTERNAL_SHARED_SECRET`, or the corresponding `[meta]` configuration setting `meta-internal-shared-secret`, is set to `true`. Default value is `false`. This value must be the same value as the meta node's `meta.internal-shared-secret`.

## Confirming LDAP connectivity


After you have configured your LDAP connectivity for InfluxDB Enterprise, you can test the connectivity by following these steps:

1. Run a quick test using `ldapsearch` to confirm your connectivity.

```
ldapsearch -p 3389 -w p@ssw0rd -x -D "CN=readonly admin,OU=Users,OU=enterprisead,DC=enterprisead,DC=example,DC=com" -h localhost -b 'OU=enterprisead,DC=enterprisead,DC=example,DC=com' '(&(CN=X_lastnames)(&(objectClass=group)))' 'member'
```

2. You should see a large block of test if everything ran correctly.

3. To see a list of usernames available to you, you can run the following:

```
$ ./ldap-populator -seed 20180321 -format ids -usercount 250000 | head
```
