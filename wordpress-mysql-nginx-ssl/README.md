# Wordpress & Nginx & SSL & Monk

This repository contains Monk.io template to deploy Wordpress system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

## Prerequisites

- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

## Make sure monkd is running

```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

## Check cluster nodes

foo@bar:~$ monk cluster peers
✔ Got the list of peers
ID                                              Name   Tag  Provider  Containers  IP           Uptime   Active  Version  Pressure
QmXiEXRNs5tSjKcYvZcX4Z71qBEs2rZeiUEYaM8wLrKHME  local       unknown   0           127.0.0.1    28m 26s  true    v3.4.3   false
QmQN8BBx7Py3yZc9sq8Zshy9mN6YPs2YNNxsCg1oJrbrtN  wp-1   wo   aws       3           52.59.97.36  16m 2s   true    v3.4.3   false

## DNS Settings

For SSL verification, we first need to redirect the domain we will use for wordpress to the server.

 `wordpress.burakhan.com   A  52.59.97.36`

## Clone Repository

```bash
git clone https://github.com/monk-io/monk-wordpress
```

## Load Template

```bash
cd wordpress-mysql-nginx-ssl
monk load MANIFEST
```

## Let's take a look at the themes I have installed

```bash
foo@bar:~$ monk list monk-wordpress-nginx-ssl
✔ Got the list
Type      Template                         Repository            Version      Tags
___________________________________________________________________________________
runnable  monk-wordpress-nginx-ssl/db            local                 -            -
group     monk-wordpress-nginx-ssl/stack         local                 -            -
runnable  monk-wordpress-nginx-ssl/wordpress     local                 -            -
runnable  monk-wordpress-nginx-ssl/nginx         local                 -            -
```

## Deploy Stack

```bash
foo@bar:~$ monk run monk-wordpress-ssl/stack
? Select tag to run [monk-wordpress-nginx-ssl/stack] on: wo
✔ Starting the job: monk-wordpress-nginx-ssl/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% docker.io/library/wordpress:latest wp-1
✔ [================================================] 100% docker.io/library/mysql:latest wp-1
✔ [================================================] 100% docker.io/whitenoi5/nginx-rev-proxy-ssl:1.15-alpine wp-1
✔ Checking/pulling images DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ Started monk-wordpress-nginx-ssl/stack

🔩 templates/local/monk-wordpress-nginx-ssl/stack
 └─🧊 Peer wp-1
    ├─🔩 templates/local/monk-wordpress-nginx-ssl/db
    │  └─📦 4f5b3690f8494f6fb24128c3796870ee-k-wordpress-nginx-ssl-db-mysql
    │     ├─🧩 docker.io/library/mysql:latest
    │     ├─💾 /var/lib/monkd/volumes/wordpress-mysql -> /var/lib/mysql
    │     └─🔌 open 52.59.97.36:3306 (0.0.0.0:3306) -> 3306
    ├─🔩 templates/local/monk-wordpress-nginx-ssl/nginx
    │  └─📦 367e0b412a575a1ff9f7bdc1d5075574-ordpress-nginx-ssl-nginx-nginx
    │     ├─🧩 docker.io/whitenoi5/nginx-rev-proxy-ssl:1.15-alpine
    │     ├─💾 /var/lib/monkd/volumes/nginx/certificates -> /usr/share/nginx/certificates
    │     ├─💾 /var/lib/monkd/volumes/nginx/certbot -> /var/www/certbot
    │     ├─🔌 open tcp 52.59.97.36:443 (0.0.0.0:443) -> 443
    │     └─🔌 open tcp 52.59.97.36:80 (0.0.0.0:80) -> 80
    └─🔩 templates/local/monk-wordpress-nginx-ssl/wordpress
       └─📦 fc81d3e6d334ff29c9889184acea7d60--nginx-ssl-wordpress-wordpress
          ├─🧩 docker.io/library/wordpress:latest
          ├─💾 /var/lib/monkd/volumes/wordpress/web -> /var/www/html
          ├─🔌 open 52.59.97.36:443 -> 443
          └─🔌 open 52.59.97.36:8088 -> 80

💡 You can inspect and manage your above stack with these commands:
 monk logs (-f) monk-wordpress-nginx-ssl/stack - Inspect logs
 monk shell     monk-wordpress-nginx-ssl/stack - Connect to the container's shell
 monk do        monk-wordpress-nginx-ssl/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

If you have forwarded the domain to the server, you can access it directly from the domain name.

<http://wordpress.burakhan.com>

## Variables

The variables are in `stack.yml` file. You can quickly setup by editing the values here.

| Variable                      | Description                                |
|------------------------------ |------------------------------------------- |
| mysql_database_user           | Database username that wordpress will use  |
| mysql_database_root_password  | Database authorized user password          |
| mysql_database_password       | Database password that wordpress will use  |
| server_name                   | The domain name you want to run            |
| mysql-image-tag               | The mysql version you want to use          |
| mysql_database_name           | Database name that wordpress will use      |
| wordpress_port                | Serve wordpress port                       |

## Stop, remove and clean up workloads and templates

```bash
monk purge -x monk-wordpress-ssl/stack monk-wordpress-ssl/db monk-wordpress-ssl/wordpress monk-wordpress-ssl/nginx
```

If you have forwarded the domain to the server, you can access it directly from the domain name.

<https://wordpress.burakhan.com>
