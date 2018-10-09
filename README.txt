
wget https://www-lipn.univ-paris13.fr/~saad/Docker-Swarm.zip
wget http://www-lipn.univ-paris13.fr/~saad/docker-machine-tuto.zip
**************************************************************************************************
		Atelier 7.1 : Multi-containers with docker-compose
**************************************************************************************************
In this tutorial, we will see how to setup a multi-container applicaion from scratch using the docker-compose (microservices appraoch).

1-Install Compose on Linux systems

https://docs.docker.com/compose/install/#install-compose

$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

$ sudo chmod +x /usr/bin/docker-compose
$ docker-compose --version

2-Create Wordpress Application

$ mkdir my_wordpress
$ cd my_wordpress
$ echo \
"version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - dbdata:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    dbdata:" >>docker-compose.yml

$ sudo docker-compose up -d
Creating network "my_wordpress_default" with the default driver
Creating volume "my_wordpress_dbdata" with default driver
Pulling db (mysql:5.7)...
5.7: Pulling from library/mysql
2a72cbf407d6: Pull complete
38680a9b47a8: Pull complete
4c732aa0eb1b: Pull complete
c5317a34eddd: Pull complete
f92be680366c: Pull complete
e8ecd8bec5ab: Pull complete
2a650284a6a8: Pull complete
5b5108d08c6d: Pull complete
beaff1261757: Pull complete
c1a55c6375b5: Pull complete
8181cde51c65: Pull complete
Digest: sha256:691c55aabb3c4e3b89b953dd2f022f7ea845e5443954767d321d5f5fa394e28c
Status: Downloaded newer image for mysql:5.7
Pulling wordpress (wordpress:latest)...
latest: Pulling from library/wordpress
2a72cbf407d6: Already exists
273cd543cb15: Pull complete
ec5ac8875de7: Pull complete
9106e19b56c1: Pull complete
ee2f70ac7c7d: Pull complete
7257ad6985e8: Pull complete
18f5c2055da2: Pull complete
85293a6fdd80: Pull complete
9e797eeb0c14: Pull complete
f16178842884: Pull complete
13899c06d3f8: Pull complete
70c27fe4c3c5: Pull complete
d32c8ad2d9d7: Pull complete
07fe445494e6: Pull complete
63b8de7b32fe: Pull complete
e4b721952e22: Pull complete
d9ede6dd6f74: Pull complete
0af4f74bfd92: Pull complete
e4e7c47b969f: Pull complete
69aff47f3112: Pull complete
Digest: sha256:201d004f55669dd2c0884f00fc44145fb0da8cafa465bf22cbaacecaf81138d4
Status: Downloaded newer image for wordpress:latest
Creating my_wordpress_db_1 ... done
Creating my_wordpress_wordpress_1 ... done

Access to wordpress http://localhost:8000

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
f851813b3beb        wordpress:latest    "docker-entrypoint.s…"   2 minutes ago       Up About a minute   0.0.0.0:8000->80/tcp   my_wordpress_wordpress_1
34f2110999eb        mysql:5.7           "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        3306/tcp               my_wordpress_db_1
$ sudo docker-compose stop


3-Create NodeJS Mongodb Application

In this tutorial, we will see How To Setup NodeJS/Express and MongoDB in Docker from scratch.

$ git clone https://github.com/walidsaad/training-app-backend.git
$ docker-compose build
mongo uses an image, skipping
Building app
Step 1/7 : FROM node:8
8: Pulling from library/node
f189db1b88b3: Pull complete
3d06cf2f1b5e: Pull complete
687ebdda822c: Pull complete
99119ca3f34e: Pull complete
e771d6006054: Pull complete
b0cc28d0be2c: Pull complete
7225c154ac40: Pull complete
7659da3c5093: Pull complete
Digest: sha256:d97f5bf54e016ad55dd0475f8710b99be14884ab1afcae8875f8092341264b0c
Status: Downloaded newer image for node:8
 ---> 6f62c0cdc461
Step 2/7 : WORKDIR /app
Removing intermediate container 3cc292fce23c
 ---> 0cb86468cffc
Step 3/7 : COPY package.json /app
 ---> 2e15b264fbe2
Step 4/7 : RUN npm install
 ---> Running in 86e09bb7c445

> uglifyjs-webpack-plugin@0.4.6 postinstall /app/node_modules/uglifyjs-webpack-plugin
> node lib/post_install.js


> nodemon@1.18.4 postinstall /app/node_modules/nodemon
> node bin/postinstall || exit 0

Love nodemon? You can now support the project via the open collective:
 > https://opencollective.com/nodemon/donate

npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN training-app@1.0.0 No description
npm WARN training-app@1.0.0 No repository field.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.4 (node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.4: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})

added 592 packages from 370 contributors and audited 10185 packages in 56.706s
found 0 vulnerabilities

Removing intermediate container 86e09bb7c445
 ---> 0478f103d788
Step 5/7 : COPY . /app
 ---> ba52d30d0cb4
Step 6/7 : CMD node app.js
 ---> Running in 420728708cd4
Removing intermediate container 420728708cd4
 ---> b82a716106cf
Step 7/7 : EXPOSE 3000
 ---> Running in abf31a995d6f
Removing intermediate container abf31a995d6f
 ---> b4654451d8b3
Successfully built b4654451d8b3
Successfully tagged training-app-backend_app:latest

$ docker-compose up
Creating network "training-app-backend_default" with the default driver
Pulling mongo (mongo:)...
latest: Pulling from library/mongo
3b37166ec614: Pull complete
504facff238f: Pull complete
ebbcacd28e10: Pull complete
c7fb3351ecad: Pull complete
2e3debadcbf7: Pull complete
004c7a04feb1: Pull complete
897284d7f640: Pull complete
af4d2dae1422: Pull complete
5e988d91970a: Pull complete
aebe46e3fb07: Pull complete
6e52ad506433: Pull complete
47d2bdbad490: Pull complete
0b15ac2388a7: Pull complete
7b8821d8bba9: Pull complete
Digest: sha256:4ad50a4f3834a4abc47180eb0c5393f09971a935ac3949920545668dd4253396
Status: Downloaded newer image for mongo:latest
Creating mongo ... done
Creating app   ... done
Attaching to mongo, app
mongo    | 2018-10-06T22:06:52.138+0000 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
mongo    | 2018-10-06T22:06:52.142+0000 I CONTROL  [initandlisten] MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=20daf644f24a
mongo    | 2018-10-06T22:06:52.142+0000 I CONTROL  [initandlisten] db version v4.0.3
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] git version: 7ea530946fa7880364d88c8d8b6026bbc9ffa48c
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.0.2g  1 Mar 2016
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] allocator: tcmalloc
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] modules: none
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] build environment:
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten]     distmod: ubuntu1604
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten]     distarch: x86_64
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten]     target_arch: x86_64
mongo    | 2018-10-06T22:06:52.143+0000 I CONTROL  [initandlisten] options: { net: { bindIpAll: true } }
mongo    | 2018-10-06T22:06:52.143+0000 I STORAGE  [initandlisten] 
mongo    | 2018-10-06T22:06:52.143+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
mongo    | 2018-10-06T22:06:52.143+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
mongo    | 2018-10-06T22:06:52.143+0000 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=7521M,session_max=20000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),statistics_log=(wait=0),verbose=(recovery_progress),
mongo    | 2018-10-06T22:06:53.256+0000 I STORAGE  [initandlisten] WiredTiger message [1538863613:256088][1:0x7fe12d07ea00], txn-recover: Set global recovery timestamp: 0
mongo    | 2018-10-06T22:06:53.425+0000 I RECOVERY [initandlisten] WiredTiger recoveryTimestamp. Ts: Timestamp(0, 0)
mongo    | 2018-10-06T22:06:53.763+0000 I CONTROL  [initandlisten] 
mongo    | 2018-10-06T22:06:53.763+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
mongo    | 2018-10-06T22:06:53.763+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
mongo    | 2018-10-06T22:06:53.763+0000 I CONTROL  [initandlisten] 
mongo    | 2018-10-06T22:06:53.764+0000 W CONTROL  [initandlisten] 
mongo    | 2018-10-06T22:06:53.764+0000 W CONTROL  [initandlisten] 
mongo    | 2018-10-06T22:06:53.764+0000 I CONTROL  [initandlisten] 
mongo    | 2018-10-06T22:06:53.766+0000 I STORAGE  [initandlisten] createCollection: admin.system.version with provided UUID: 2f55d640-bd37-4d8b-a2a7-ccf0fdb41fd5
mongo    | 2018-10-06T22:06:53.992+0000 I COMMAND  [initandlisten] setting featureCompatibilityVersion to 4.0
mongo    | 2018-10-06T22:06:54.000+0000 I STORAGE  [initandlisten] createCollection: local.startup_log with generated UUID: abe80a82-8ed1-4d02-90f6-eda0d416195a
mongo    | 2018-10-06T22:06:54.150+0000 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory '/data/db/diagnostic.data'
app      | Server started at port:3000
mongo    | 2018-10-06T22:06:54.153+0000 I NETWORK  [initandlisten] waiting for connections on port 27017
mongo    | 2018-10-06T22:06:54.153+0000 I STORAGE  [LogicalSessionCacheRefresh] createCollection: config.system.sessions with generated UUID: 5f34a106-c8a8-475a-80b3-c1de6f62ec1c
mongo    | 2018-10-06T22:06:54.194+0000 I NETWORK  [listener] connection accepted from 172.18.0.3:40463 #1 (1 connection now open)
mongo    | 2018-10-06T22:06:54.204+0000 I NETWORK  [conn1] received client metadata from 172.18.0.3:40463 conn1: { driver: { name: "nodejs", version: "2.2.34" }, os: { type: "Linux", name: "linux", architecture: "x64", version: "3.16.0-4-amd64" }, platform: "Node.js v8.12.0, LE, mongodb-core: 2.1.18" }
app      | Connected to mongodb server at 27017 port
mongo    | 2018-10-06T22:06:54.433+0000 I INDEX    [LogicalSessionCacheRefresh] build index on: config.system.sessions properties: { v: 2, key: { lastUse: 1 }, name: "lsidTTLIndex", ns: "config.system.sessions", expireAfterSeconds: 1800 }
mongo    | 2018-10-06T22:06:54.433+0000 I INDEX    [LogicalSessionCacheRefresh] 	 building index using bulk method; build may temporarily use up to 500 megabytes of RAM
mongo    | 2018-10-06T22:06:54.435+0000 I INDEX    [LogicalSessionCacheRefresh] build index done.  scanned 0 total records. 0 secs
mongo    | 2018-10-06T22:06:54.435+0000 I COMMAND  [LogicalSessionCacheRefresh] command config.$cmd command: createIndexes { createIndexes: "system.sessions", indexes: [ { key: { lastUse: 1 }, name: "lsidTTLIndex", expireAfterSeconds: 1800 } ], $db: "config" } numYields:0 reslen:114 locks:{ Global: { acquireCount: { r: 2, w: 2 } }, Database: { acquireCount: { w: 2, W: 1 } }, Collection: { acquireCount: { w: 2 } } } protocol:op_msg 282ms



==>Access to application (nodejs server) http://localhost:3000/
