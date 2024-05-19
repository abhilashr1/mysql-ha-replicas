# mysql-ha-replicas

### Configuring replication

First exec into the master container and enable replication

```
docker exec -it mysql-master mysql -u root -p
```

In the mysql shell
```
CREATE USER 'replica_user'@'%' IDENTIFIED WITH mysql_native_password BY 'pass';
GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
```

Now for each replica, exec into the container and connect to master

```
docker exec -it mysql-replica1 mysql -u root -p
```

In the replica's mysql shell

```
CHANGE MASTER TO
    MASTER_HOST='mysql-master',
    MASTER_USER='replica_user',
    MASTER_PASSWORD='pass',
    MASTER_LOG_FILE='mysql-bin.000003 (copy from master)', 
    MASTER_LOG_POS=12345 (copy from master...);               
START SLAVE;
```

Verify by checking status (IO):

```
SHOW SLAVE STATUS \G;
```

