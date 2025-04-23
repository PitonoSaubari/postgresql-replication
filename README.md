# PostgreSQL Replication

A sample Master-Slave Replication from PostgreSQL instance to other PostgreSQL instance.
Both PostgreSQL instance running on Docker.

## Instance details

First instance of PostgreSQL (master)
name: db01
mapped path: $PWD/dev/postgresql-replication/db1-data/

Second instance of PostgreSQL (slave)
name: db02
mapped path: $PWD/dev/postgresql-replication/db2-data/

Please create mapped path before starting db01 and db02 docker.
