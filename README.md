Docker running Nginx, PHP-FPM, MySQL and PHPMyAdmin.

### Images to use

* [Nginx](https://hub.docker.com/_/nginx/)
* [MySQL](https://hub.docker.com/_/mysql/)
* [PHP-FPM](https://hub.docker.com/r/nanoninja/php-fpm/)
* [PHPMyAdmin](https://hub.docker.com/r/phpmyadmin/phpmyadmin/)
* [Generate Certificate](https://hub.docker.com/r/jacoelho/generate-certificate/)

You should be careful when installing third party web servers such as MySQL or Nginx.

This project use the following ports :

| Server     | Port |
|------------|------|
| MySQL      | 3306 |
| PHPMyAdmin | 8080 |
| Nginx      | 80   |
| Nginx SSL  | 443  |

---


## Clone the project

To install [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git), download it and install following the instructions : 

```sh
git clone https://github.com/nisaofee/docker-lemp.git
```

Go to the project directory : 

```sh
cd docker-lemp
```


---

## Configure Nginx With SSL Certificates

1. Generate SSL certificates

    ```sh
    sudo docker run --rm -v $(pwd)/etc/ssl:/certificates -e "SERVER=localhost" jacoelho/generate-certificate
    ```

2. Configure Nginx

    Edit nginx file `etc/nginx/default.conf` and uncomment the SSL server section :

    ```sh
    # server {
    #     server_name localhost;
    #
    #     listen 443 ssl;
    #     ...
    # }
    ```

## Run the application

1. Copying the composer configuration file : 

    ```sh
    cp web/app/composer.json.dist web/app/composer.json
    ```

2. Start the application :

    ```sh
    sudo docker-compose up -d
    ```

    **Please wait this might take a several minutes...**

    ```sh
    sudo docker-compose logs -f # Follow log output
    ```

3. Open your favorite browser :

    * [http://localhost](http://localhost/)
    * [https://localhost:443](https://localhost:443/) ([HTTPS](#configure-nginx-with-ssl-certificates) not configured by default)
    * [http://localhost:8080](http://localhost:8080/) PHPMyAdmin (username: dev, password: dev)

4. Stop and clear services

    ```sh
    sudo docker-compose down -v
    ```


#### Backup of database

```sh
mkdir -p data/db/dumps
```

```sh
source .env && sudo docker exec $(shell docker-compose ps -q mysqldb) mysqldump --all-databases -u"$MYSQL_ROOT_USER" -p"$MYSQL_ROOT_PASSWORD" > "data/db/dumps/db.sql"
```

or

```sh
source .env && sudo docker exec $(shell docker-compose ps -q mysqldb) mysqldump test -u"$MYSQL_ROOT_USER" -p"$MYSQL_ROOT_PASSWORD" > "data/db/dumps/test.sql"
```

#### Restore Database

```sh
source .env && sudo docker exec -i $(sudo docker-compose ps -q mysqldb) mysql -u"$MYSQL_ROOT_USER" -p"$MYSQL_ROOT_PASSWORD" < "data/db/dumps/db.sql"
```