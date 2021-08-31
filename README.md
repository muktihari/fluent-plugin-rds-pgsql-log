# Amazon RDS for PostgreSQL log input plugin for fluentd

[![Gem Version](https://badge.fury.io/rb/fluent-plugin-rds-pgsql-log.svg)](https://badge.fury.io/rb/fluent-plugin-rds-pgsql-log)

## Overview
- Amazon Web Services RDS log input plugin for fluentd

## Installation

    $ fluentd-gem install fluent-plugin-rds-pgsql-log

## AWS ELB Settings
- settings see: [PostgreSQL Database Log Files](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.PostgreSQL.html)

## When SSL certification error
log:
```
SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed
```
Do env setting follows:
```
SSL_CERT_FILE=/etc/ssl/certs/ca-bundle.crt (If you using amazon linux)
```

## Configuration

```config
<source>
  type rds_pgsql_log
  # required
  region                 <region name>
  db_instance_identifier <instance identifier>
  # optional if you can IAM credentials
  access_key_id          <access_key>
  secret_access_key      <secret_access_key>
  # optional
  refresh_interval       <interval number by second(default: 30)>
  tag                    <tag name(default: rds-pgsql.log>
  pos_file               <log getting position file(default: rds-pgsql.log)>
</source>
```

### Example setting
```config
<source>
  type rds_pgsql_log
  region ap-northeast-1
  db_instance_identifier test-postgres
  access_key_id     XXXXXXXXXXXXXXXXXXXX
  secret_access_key xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  refresh_interval  30
  tag pgsql.log
  pos_file /tmp/pgsql-log-pos.dat
</source>

<match pgsql.log>
  type stdout
</match>
```

### json output example
```json
{
  "time": "2021-08-30 10:55:27 UTC",
  "host": "192.168.30.175(53092)",
  "user": "testuser",
  "database": "db1",
  "pid": "9769",
  "duration": 600.89,
  "duration_unit": "ms",
  "statement": "execute lrupsc_1549_0",
  "message_level": "LOG",
  "message": "SELECT t.id FROM t JOIN t2 ON t2.t_id = t.id WHERE t2.content::jsonb @> $1::jsonb LIMIT 1",
  "log_file_name":"error/postgresql.log.2021-08-30-19"
}
```

## Changes
### 
- Only log statement instead of all database log
- Update Regex to parse new fields: statement, duration and duration_unit. This way, message only contains SQL Query.

### 0.2.0
- require fluentd version 0.14.0

### 0.3.0
- use AWS SDK v3.x

### 0.4.0
- Use record time

