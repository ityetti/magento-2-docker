![Magento 2](https://cdn.rawgit.com/rafaelstz/magento2-snippets-visualstudio/master/images/icon.png)

#  Magento 2 Docker to Development (Apple Silicon)

### Traefik + Nginx + Redis + PHP-FPM + MySQL + XDebug + Mailpit + RabbitMQ + OpenSearch + Varnish

The docker stack is composed of the following containers

| Name                 | Version |
|----------------------|---------|
| traefik              | 3.2     |
| nginx                | 1.22    |
| php-fpm              | 8.1     |
| php-fpm-xdebug       | 3.1.5   |
| redis                | 6.2     |
| mysql                | 8.0.34  |
| mailpit              | 1.21    |
| rabbitmq             | 3.9     |
| opensearch           | 1.2.4   |
| opensearch-dashboard | 1.2.0   |
| varnish              | 7.1     |

### Container traefik
Starts a reverse proxy and load balancer for project<br>
Opens local port: `80`, `443`

### Container nginx
Builds from the nginx folder. <br>
Mounts the folder magento2 from the project main folder into the container volume `/home/magento`.<br>

### Container php-fpm
Builds from the php-fpm folder.<br>
Mounts the folder magento2 from the project main folder into the container volume `/home/magento`.<br>
This container includes all dependencies for Magento 2.<br>

### Container php-fpm-xdebug
Builds from the php-fpm-xdebug folder.<br>
Mounts the folder magento2 from the project main folder into the container volume `/home/magento`.<br>
This container includes all dependencies for Magento 2 (also contain xDebug).<br>

### Container redis:
Starts a redis container.<br>

### Container mysql:
Please change or set the mysql environment variables
    
    MYSQL_DATABASE: 'xxxx'
    MYSQL_ROOT_PASSWORD: 'xxxx'
    MYSQL_USER: 'xxxx'
    MYSQL_PASSWORD: 'xxxx'
    MYSQL_ALLOW_EMPTY_PASSWORD: 'xxxxx'

Default values:

    MYSQL_DATABASE: 'magento_db'
    MYSQL_ROOT_PASSWORD: 'root_pass'
    MYSQL_USER: 'magento_user'
    MYSQL_PASSWORD: 'PASSWD#'
    MYSQL_ALLOW_EMPTY_PASSWORD: 'false'

Opens up port: `3306`

Note: On your host, port 3306 might already be in use. So before running docker-compose.yml, under the docker-compose.yml's mysql section change the host's port number to something other than 3306, select any as long as that port is not already being used locally on your machine.

### Container mailpit:
Starts a mailpit container.<br>
Opens up port: `8025`

### Container rabbitmq:
Starts a rabbitmq container.<br>
Opens up port: `15672`

### Container opensearch:
Starts an opensearch container.<br>

### Container opensearch-dashboard:
Starts an opensearch dashboard container.<br>
Opens up port: `5601`

### Container varnish:
Builds from the varnish folder.
Starts a varnish container.<br>
Opens up port: `6081`

## Setup
Copy your `.env.sample` to `.env` file in root folder, and change `PROJECT_NAME` and `PROJECT_VIRTUAL_HOST`:<br>
`PROJECT_NAME` - help you to create simple and clear container names.<br>
`PROJECT_VIRTUAL_HOST` - it is your main url address.<br>

For example:

    PROJECT_NAME=magento2
    PROJECT_VIRTUAL_HOST=magento2.test

Edit your `/etc/hosts` and add next line:<br>
`127.0.0.1 magento2.test traefik.magento2.test mail.magento2.test search.magento2.test dashboard.magento2.test rabbit.magento2.test`<br>

To start/build the stack.<br>
Use - `docker-compose up` or `docker-compose up -d` to run the container on detached mode.<br>
Compose will take some time to execute.<br>
After the build has finished you can press the ctrl+c and docker-compose stop all containers.

## Installing Magento
You will check the latest version of Magento from link: https://magento.com/tech-resources/download <br>
To the run installation process use next commands.<br>
Create new project:

    ./scripts/composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.4-p11 /home/magento
Install project (don't forget to change **--base-url** to yours):

    ./scripts/magento setup:install --base-url=https://magento2.test/ --db-host=mysql --db-name=magento_db --db-user=magento_user --db-password="PASSWD#" --admin-firstname=admin --admin-lastname=admin --admin-email=admin@admin.test --admin-user=admin --admin-password=admin1! --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=opensearch --elasticsearch-port=9200

## Setting up Magento
To access the magento homepage, go to the following url: https://magento2.test<br>

## How to use xDebug
You could enable or disable xDebug with the next command: `./scripts/switch_mode [fpm|xdebug]`<br>
`fpm` - Enable container without xDebug <br>
`xdebug` - Enable container with xDebug <br>


Also, you can open:<br>
https://traefik.magento2.test - **Traefik Dashboard** (traefik/traefik123 for access)<br>
https://mail.magento2.test - **Mailpit**<br>
https://search.magento2.test - **OpenSearch**<br>
https://dashboard.magento2.test - **OpenSearch Dashboard**<br>
https://rabbit.magento2.test - **RabbitMQ** (guest/guest for access)<br>

## Feature Updates
- v1.0.0 - Stable release
- v1.0.1 - Updated to PHP 7.4.x, added docker-sync for macOS users
- v1.0.2 - Fix xDebug, add rabbitmq management, fix email sending
- v1.0.3 - Updated to PHP 8.1.x
- v1.0.4 - Fix xDebug for stable work
- v1.0.5 - Replace Elasticsearch to OpenSearch, upgrade component versions, added varnish
- v1.0.6 - Fix xDebug for correct stopping at point
- v1.0.7 - Add traefik, optimization for varnish, remove nginx-proxy
- v1.0.8 - Replace mailhog to mailpit
- v1.0.9 - Add n98-magerun2
- v1.1.0 - Add a switcher for PHP that enables or disables xDebug

## Branches
| Name                 | Magento versions                       |
|----------------------|----------------------------------------|
| master               | 2.4.6 and higher                       |
| m244                 | 2.4.4 up to 2.4.6                      |
| develop              | like master with untested improvements |