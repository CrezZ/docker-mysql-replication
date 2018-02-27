# Docker image for MySQL master-slave replication remote hosts

forked from  Actency/docker-mysql-replication - its a great scripts!

## Additional environment variables:

* MYSQL_ROOT_PASSWORD [default: empty] 
* MYSQL_USER [default: empty] !!Mandatory
* MYSQL_PASSWORD [default: empty] !!Mandatory
* MYSQL_DATABASE [default: empty] !!Mandatory

FOR cluser: 

* REPLICATION_USER [default: replication] !! Mandatory for slave
* REPLICATION_PASSWORD [default: replication_pass] !! Mandatory for slave
* REPLICATION_HEALTH_GRACE_PERIOD [default: 3]
* REPLICATION_HEALTH_TIMEOUT [default: 10] 
* MASTER_PORT [default: 3306] !! Mandatory for slave
* MASTER_HOST [default: master] !! Mandatory for slave

## If you already have a master you need:

1. Change config (/etc/mysql/my.conf)
add next lines (if nothing) 
```
server-id=1
```
2 Create replicate user. ALL of this priveleges need to automated dump database to slave
```
mysql -u root -p
GRANT SELECT, RELOAD, SUPER, LOCK TABLES, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

## OR Create NEW master  (if need)

! You need to create path to store a database files. (/data/mastermysql in this example). This dir MUST be empty in first run
```
docker run -d \
  -v /data/mastermysql:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=mysqlroot \
 -e MYSQL_USER=example_user \
 -e MYSQL_PASSWORD=mysqlpwd \
 -e MYSQL_DATABASE=example \
 -e REPLICATION_USER=replication_user \
 -e REPLICATION_PASSWORD=myreplpassword \
 crezz/docker-mysql-replication:5.7

```

## Start slave

! You need to create path to store a database files. (/data/slavemysql in this example). This dir MUST be empty in first run.
! MYSQL_USER, MYSQL_PASSWORD, MYSQL_DATABASE MUST be same as on master host.
```
docker run -d \
  -v /data/slavemysql:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=mysqlroot \
 -e MYSQL_USER=example_user \
 -e MYSQL_PASSWORD=mysqlpwd \
 -e MYSQL_DATABASE=example \
 -e REPLICATION_USER=replication_user \
 -e REPLICATION_PASSWORD=myreplpassword \
 -e MATSRE_PORT=3306 \
 -e MASTER_HOST=IP_OF_MASTER \
  crezz/docker-mysql-replication:5.7
```

## Check replication status

```
docker exec -it mysql_slave mysql -uroot -p -e "SHOW SLAVE STATUS\G;"
```
