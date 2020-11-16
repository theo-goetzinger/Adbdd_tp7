## Théo Goetzinger

## TP7

## node1.cnf

```
[mariadb]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_address="gcomm://node-1,node-2,node-3"
wsrep_node_address="node1"
wsrep_node_name="node1"
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
innodb_doublewrite=1
query_cache_size=0
wsrep_on=ON

```

## node2.cnf

```
[mariadb]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_address="gcomm://node-1,node-2,node-3"
wsrep_node_address="node2"
wsrep_node_name="node2"
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
innodb_doublewrite=1
query_cache_size=0
wsrep_on=ON

```

## node3.cnf

```
[mariadb]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_address="gcomm://node-1,node-2,node-3"
wsrep_node_address="node3"
wsrep_node_name="node3"
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
innodb_doublewrite=1
query_cache_size=0
wsrep_on=ON


```


## docker-compose.yml

```
version: '3.7'

services:
 node1:
  image: mariadb
  restart: on-failure
  command: --wsrep-new-cluster
  ports:
   - 3306
   - 4444
   - 4567
   - 4568
  networks:
   - internals
  environement:
   MYSQL_ROOT_PASSWORD: ubuntu
  volumes:
   - ./maria-node1:/var/lib/mysql
   - ./config/tp.sql:/sql/tp.sql
   - ./config/node1.cnf:/etc/mysql/mariadb.conf.d/node1.cnf

 node2:
  image: mariadb
  #command: --wsrep-new-cluster
  ports:
   - 3306
   - 4444
   - 4567
   - 4568
  networks:
   - internals
  environement:
   MYSQL_ROOT_PASSWORD: ubuntu
  volumes:
   - ./maria-node2:/var/lib/mysql
   - ./config/node2.cnf:/etc/mysql/mariadb.conf.d/node2.cnf

 node3:
  image: mariadb
  #command: --wsrep-new-cluster
  ports:
   - 3306
   - 4444
   - 4567
   - 4568
  networks:
   - internals
  environement:
   MYSQL_ROOT_PASSWORD: ubuntu
  volumes:
   - ./maria-node3:/var/lib/mysql
   - ./config/node3.cnf:/etc/mysql/mariadb.conf.d/node3.cnf

networks:
 internals:
 
```

## lancement du docker-compose
```
sudo docker-compose up -d

```

## Lancer du container node1
```
sudo docker-compose exec node1 bash
```

## Connexion à mariadb et création de la db

```
mysql -u root -p

CREATE DATABASE TP7_test;
USE TP7_test;

CREATE TABLE infos (
    nom VARCHAR(255),
    prenom VARCHAR(255),
    ville VARCHAR(255)
);

INSERT INTO infos VALUES ("wejdene", "catherine", "Bordeaux"), ("bob", "marley", "Bordeaux"), ("Jacques", "jean", "Bordeaux");

SELECT * FROM infos;

```

## Véfication de la réplication
```
docker-compose exec node2 bash
mysql -u root -p

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| TP7_test           |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.001 sec)

MariaDB [(none)]>

``` 



