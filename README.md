## Guacamole Database Image

[![](https://images.microbadger.com/badges/image/khirin/guacamole-db.svg)](https://microbadger.com/images/khirin/guacamole-db "Get your own image badge on microbadger.com")

### Description
This is my minimal customized Guacamole Database image based on Alpine (with [my alpine image](https://hub.docker.com/r/khirin/alpine/)).
No root process.
The start.sh script will create the first database, a standard user that can interact from a specified host and network with the DB container.
If you have some sql script (to import data) to execute, put it at /conf/$DB_init.sql. It will be launch with the init_db.sh script.

### Guacamole Images
• Client Part : [khirin/guacamole-client](https://hub.docker.com/r/khirin/guacamole-client/)
• Server Part : [khirin/guacamole-server](https://hub.docker.com/r/khirin/guacamole-server/)
• DB Part : [khirin/guacamole-db](https://hub.docker.com/r/khirin/guacamole-db/)

### Packages
• Packages from [khirin/alpine](https://hub.docker.com/r/khirin/alpine/)
• mariadb
• mariadb-client

### Default Configuration
• Configuration from [khirin/alpine](https://hub.docker.com/r/khirin/alpine/)
• Default user (UID) : mysql (2000)
• Default group (GID) : mysql (2000)
• Default port : 3306
• Database name : guacamole
• DB Standard User : guacamole
• DB Standard Password : userpassword
• DB Root Password : rootpassword
**DB password must be set with [base64](https://www.base64encode.org/) coding.**

### Volumes
• /scripts (RO) : Start script and db init script. 
• /conf (RO) : SQL scripts.
• /var/lib/mysql (RW) : Where all database data will be store.
```shell
docker volume create --name data_guacamoledb
```

### Network
• guacamole-network : Network with only the guacamole-db container and the guacamole-client container.
```shell
docker network create -o "com.docker.network.bridge.name=guacamole" guacamole-network
```
### Usage
• Run : Will use the default configuration above.
• Build : Example of custom build. You can also directly modify the Dockerfile (I won't be mad, promis !)
• Create : Example of custom create. It is useless to publish the port, expose it is enough to other container(s) on the same network.

##### • Run
```shell
docker run --detach \
                -v "data_guacamoledb:/var/lib/mysql:rw" \
                       -v "/my_script_folder:/scripts:ro" \
                       -v "/my_conf_folder:/conf:ro" \
			--network guacamole-network \
			khirin/guacamole-db`
```

##### • Build
```shell
/bin/docker build \
                --no-cache=true \
                --force-rm \
                --build-arg UID="2000" \
                --build-arg GID="2000" \
                --build-arg PORT="3306" \
                -t repo/guacamole-db .
```

##### • Create
```shell
/bin/docker create --hostname=guacamole-db \
                --name guacamole-db \
                -v "data_guacamoledb:/var/lib/mysql:rw" \
                -v "/my_script_folder:/scripts:ro" \
                -v "/my_conf_folder:/conf:ro" \
                --env DATABASE="guacamole" \
                --env DB_USER="guacamole" \
                --env DB_USER_PASSWORD="Z3VhY2Ftb2xlCg===" \
                --env DB_ROOT_PASSWORD="cm9vdHBhc3N3b3JkCg==" \
                --env CLIENT_HOST="guacamole-client.guacamole-network" \
                -m 128M --memory-swap 256M \
                --network guacamole-network \
                repo/guacamole-db
```

### Author
khirin : [DockerHub](https://hub.docker.com/u/khirin/), [GitHub](https://github.com/khirin?tab=repositories)

### Thanks
All my images are based on my personal knowledge and inspired by many projects of the Docker community.
If you recognize yourself in some working approaches, you might be one of my inspirations (Thanks!).


