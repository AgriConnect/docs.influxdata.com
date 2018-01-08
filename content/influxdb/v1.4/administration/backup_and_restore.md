---
title: Backup and Restore

menu:
  influxdb_1_4:
    weight: 30
    parent: administration
---

## Overview
New in version 1.5, the backup utility provides the option to run both backup and restore 
functions on a live database. It also provides features to backup and restore single or multiple
databases, along with optional filtering based on data point time stamps. Finally, the backup tool supports 
import of data from an [InfluxEnterprise](/enterprise/latest/) cluster, and it can also generate backup files
that may be imported into an enterprise database.  The offline backup/restore functions
provided in influxdb versions 1.4 and earlier are retained in version 1.5 without change, and 
are detailed in the section titled [Backward Compatible Offline Backup/Restore](#legacy).  

> **Note:** Prior to version 1.5, the open source backup tool created a different backup file format than 
the enterprise version of influxdb. This document will refer to that format as the _legacy_ format.  The legacy
format is fully supported, so that it may be provided as input to the new online restore function.  If creating a
new backup process from scratch, we recommend using the new enterprise-compatible backup format, which uses less 
disk space and also provides a clear transfer path for data between the enterprise and open source versions 
of influxdb.  
>

<h2 id="online">Enterprise Compatible Online Backup and Restore</h2>

<h3 id="remoteconn">Configuring a Remote Connection</h3>
The online backup and restore processes execute over a TCP connection to the database.  To enable 
the port for the backup/restore service: 

**1.** At the root level of the influxdb config file, uncomment the [`bind-address` configuration setting](/influxdb/v1.4/administration/config/#bind-address-127-0-0-1-8088) on the remote node  
**2.** Update the `bind-address` value to `<remote-node-IP>:8088`  
**3.** Provide the IP and port to the `-host` parameter when you run commands, for example:

```
$ influxd backup -database mydatabase -host <remote-node-IP>:8088 /tmp/mysnapshot
```

### Backup
The improved backup command is similar to previous versions of influxdb, except that it  
which generates backups in an enterprise compatible format and has some new filtering options
to constrain the range of data points that are exported to the backup.  
It is invoked by the `influxd` binary using the -enterprise flag: 

```
influxd backup -enterprise [options] <path-to-backup>
```

**Backup Arguments**
- `-host <host:port>`- The host to connect to and perform a snapshot of. Defaults to '127.0.0.1:8088'.

- `-database <name>`- The database to backup. Required.

- `-retention <name>` - The retention policy to backup. Optional.

- `-shard <id>` - The shard id to backup. Optional. If specified, '-retention <name>' is required.

- `-since <2015-12-24T08:12:13Z>` - Do an incremental backup since the passed in time. The time needs to be in the RFC3339 format. Optional.

- `-start <2015-12-24T08:12:23Z>` - All points earlier than this time stamp will be excluded from the export. Not compatible with -since.
- `-end <2015-12-24T08:12:23Z>` - All points later than this time stamp will be excluded from the export. Not compatible with -since.
- `-enterprise` - Generate backup files in the format used for influxdb enterprise.

### Restore
An online restore process is initiated by using either the -enterprise or -online flags.  The flags indicate that the input is 
in either an enterprise backup format, or a legacy backup format, respectively.  It has the following options: 

- `-host <host:port>`- The host to connect to and perform a snapshot of. Defaults to '127.0.0.1:8088'.

- `-db    <name>`- Identifies the database from the backup that will be restored.

- `-newdb <name>`- The name of the database into which the archived data will be imported on the target system.
	        If not given, then the value of -db is used.  The new database name must be unique to the target system.

- `-rp    <name>`- Identifies the retention policy from the backup that will be restored.  Requires that -db is set.

- `-newrp <name>`- The name of the retention policy that will be created on the target system. Requires that -rp is set.
	        If not given, the value of -rp is used.

- `-shard <id>`- Optional.  If given, -db and -rp are required.  Will restore the single shard's data.

> **Note:** Even if you have previous backup automation that supports the legacy format, you may wish to 
test the new online feature for legacy backups.  It enables restoration of a single database to a running 
instance, while leaving all existing data on the server in place.  The offline restore method will clear 
all existing databases on the server.  
>

<h2 id="legacy">Backward Compatible Offline Backup/Restore</h2>

InfluxDB has the ability to snapshot an instance at a point-in-time and restore it.
All backups are full backups.
InfluxDB does not yet support incremental backups.
There are two types of data to backup, the metastore and the metrics themselves.
The [metastore](/influxdb/v1.4/concepts/glossary/#metastore) is backed up in its entirety.
The metrics are backed up per-database in a separate operation from the metastore backup.

If you are working with an InfluxEnterprise cluster, please see the [Backup
and Restore Guide](/enterprise/latest/guides/backup-and-restore/) in the
InfluxEnterprise documentation.

### Backing up the Metastore

InfluxDB's metastore contains internal information about the status of
the system, including user information, database/shard metadata, CQs, RPs,
and subscriptions. While a node is running, you can
create a backup of your instance's metastore by running the command:

```
influxd backup <path-to-backup>
```

Where `path-to-backup` can be replaced with the directory where you
would like the backup to be written to. Without any other arguments,
the backup will only record the current state of the system
metastore. For example, the command:

```bash
$ influxd backup /tmp/backup
2016/02/01 17:15:03 backing up metastore to /tmp/backup/meta.00
2016/02/01 17:15:03 backup complete
```

Will create a metastore backup in the directory `/tmp/backup` (the
directory will be created if it doesn't already exist).

### Backing up a Database

Each database must be backed up individually.

To backup a database, you will need to add the `-database` flag:

```bash
influxd backup -database <mydatabase> <path-to-backup>
```

Where `mydatabase` is the name of the database you would like to
backup, and `path-to-backup` is where the backup data should be
stored. Optional flags also include:

- `-retention <retention policy name>` - This flag can be used to
  backup a specific retention policy. For more information on
  retention policies, please see
  [here](/influxdb/v1.4/query_language/database_management/#retention-policy-management). If
  not specified, all retention policies will be backed up.

- `-shard <shard ID>` - This flag can be used to backup a specific
  shard ID. To see which shards are available, you can run the command
  `SHOW SHARDS` using the InfluxDB query language. If not specified,
  all shards will be backed up.

- `-since <date>` - This flag can be used to create a backup _since_ a
  specific date, where the date must be in
  [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) format (for example,
  `2015-12-24T08:12:23Z`). This flag is important if you would like to
  take incremental backups of your database. If not specified, all
  timeranges within the database will be backed up.

> **Note:** Metastore backups are also included in per-database backups

As a real-world example, you can take a backup of the `autogen`
retention policy for the `telegraf` database since midnight UTC on
February 1st, 2016 by using the command:

```
$ influxd backup -database telegraf -retention autogen -since 2016-02-01T00:00:00Z /tmp/backup
2016/02/01 18:02:36 backing up rp=default since 2016-02-01 00:00:00 +0000 UTC
2016/02/01 18:02:36 backing up metastore to /tmp/backup/meta.01
2016/02/01 18:02:36 backing up db=telegraf rp=default shard=2 to /tmp/backup/telegraf.default.00002.01 since 2016-02-01 00:00:00 +0000 UTC
2016/02/01 18:02:36 backup complete
```

Which will send the resulting backup to `/tmp/backup`, where it can
then be compressed and sent to long-term storage.

### Remote Backups

The legacy backup mode also supports live, remote backup functionality.  
Follow the directions in [Configuring a Remote Connection](#remoteconn) to configure this feature.  

## Restore

To restore a backup, you will need to use the `influxd restore` command.

> **Note:** Restoring from backup is only supported while the InfluxDB daemon is stopped.

To restore from a backup you will need to specify the type of backup,
the path to where the backup should be restored, and the path to the backup.
The command:

```
influxd restore [ -metadir | -datadir ] <path-to-meta-or-data-directory> <path-to-backup>
```

The required flags for restoring a backup are:

- `-metadir <path-to-meta-directory>` - This is the path to the meta
  directory where you would like the metastore backup recovered
  to. For packaged installations, this should be specified as
  `/var/lib/influxdb/meta`.

- `-datadir <path-to-data-directory>` - This is the path to the data
  directory where you would like the database backup recovered to. For
  packaged installations, this should be specified as
  `/var/lib/influxdb/data`.

The optional flags for restoring a backup are:

- `-database <database>` - This is the database that you would like to
  restore the data to. This option is required if no `-metadir` option
  is provided.

- `-retention <retention policy>` - This is the target retention policy
  for the stored data to be restored to.

- `-shard <shard id>` - This is the shard data that should be
  restored. If specified, `-database` and `-retention` must also be
  set.

Following the backup example above, the backup can be restored in two
steps. First, the metastore needs to be restored so that InfluxDB
knows which databases exist:

```
$ influxd restore -metadir /var/lib/influxdb/meta /tmp/backup
Using metastore snapshot: /tmp/backup/meta.00
```

Once the metastore has been restored, we can now recover the backed up
data. In the real-world example above, we backed up the `telegraf`
database to `/tmp/backup`, so let's restore that same dataset. To
restore the `telegraf` database:

```
$ influxd restore -database telegraf -datadir /var/lib/influxdb/data /tmp/backup                                                                         
Restoring from backup /tmp/backup/telegraf.*
unpacking /var/lib/influxdb/data/telegraf/default/2/000000004-000000003.tsm
unpacking /var/lib/influxdb/data/telegraf/default/2/000000005-000000001.tsm
```

> **Note:** Once the backed up data has been recovered, the
permissions on the shards may no longer be accurate. To ensure
the file permissions are correct, please run:

> `$ sudo chown -R influxdb:influxdb /var/lib/influxdb`

Once the data and metastore are recovered, it's time to start the database:

```bash
$ service influxdb start
```

As a quick check, we can verify the database is known to the metastore
by running a `SHOW DATABASES` command:

```
influx -execute 'show databases'
name: databases
---------------
name
_internal
telegraf
```

The database has now been successfully restored!
