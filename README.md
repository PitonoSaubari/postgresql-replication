# PostgreSQL Replication

A sample Master-Slave Replication from PostgreSQL instance to other PostgreSQL instance.
Both PostgreSQL instance running on Docker.

## Instance details

First instance of PostgreSQL (master)  
name: db1  
mapped path: $PWD/dev/postgresql-replication/db1-data/  

Second instance of PostgreSQL (slave)  
name: db2  
mapped path: $PWD/dev/postgresql-replication/db2-data/  

Please create mapped path before starting db01 and db02 docker.

## Master instance (db1)

Start docker instance of db1

    sudo docker compose up -d db1

Test connection to employees database on db1

    sudo docker exec -it db1 psql -U postgres -W

Create role for replication

    create role replica_user with replication login password 'repl1234';

## Slave instance (db2)

Start docker instance of db2

    sudo docker compose up -d db2

Test connection to employees database on db2

    sudo docker exec -it db2 psql -U postgres -W

Create backup of db1

    sudo docker exec -it db2 bash
    pg_basebackup -h db1 -U replica_user -X stream -C -S replica_1 -v -R -W -D /tmp/data/db/

Stop db2

    sudo docker compose down db2

On host server, replace db2 data with backup from db1

    /bin/rm -rf $PWD/db2-data/*
    cp -av $PWD/data/db/* $PWD/db2-data/
    chown 999 -R $PWD/db2-data/*

Start db2

    sudo docker compose up -d db2

## Verify Replication

### on db1

Check replication status

    sudo docker exec -it db1 psql -U postgres -W
    select client_addr, state from pg_stat_replication;

Verify the state of replication is streaming  
  
Import data to db1

    sudo docker exec -it db1 psql -U postgres -W -f /tmp/data/periodic_table.sql

### on db2

Verify data is replicated to db2

    sudo docker exec -it db2 psql -U postgres -W
    select count(*) from periodic_table;

## Data 

Periodic table data was used in this project

Source: https://github.com/andrejewski/periodic-table
License: ISC License






### to delete

Import / load data to mysql01

    sudo docker exec -it db1 bash
    cd /tmp/data
    mysql -uroot -p"root1234" employees < employees.sql
    mysql -uroot -p"root1234" mysql
    grant all on employees.* to 'master'@'%';
    flush privileges;

Verify credential is correct and database is populated

    mysql -umaster -p"password1234" employees
    show tables;
    select count(*) from employees;
    select * from employees limit 5;

Verify database can be updated

    update employees set first_name='George' where emp_no=10001;
    select * from employees where emp_no=10001;
    update employees set first_name='Georgi' where emp_no=10001;
    select * from employees where emp_no=10001;

### end of to delete

