# Wordpress & Nginx & Monk
This repository contains Monk.io template to deploy Wordpress system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

# Prerequisites
- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

#### Make sure monkd is running.
```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

## Clone Repository
```bash
git clone https://github.com/monk-io/monk-wordpress
```

## Load Template
```bash
cd wordpress-mysql-nginx
monk load MANIFEST
```


#### Let's take a look at the themes I have installed.
```bash
foo@bar:~$ monk list monk-wordpress-nginx
✔ Got the list
Type      Template                         Repository            Version      Tags
___________________________________________________________________________________
runnable  monk-wordpress-nginx/db                local                 -            -
group     monk-wordpress-nginx/stack             local                 -            -
runnable  monk-wordpress-nginx/wordpress         local                 -            -
runnable  monk-wordpress-nginx-ssl/nginx         local                 -            -

```


## Deploy Stack
```bash
foo@bar:~$ monk run monk-wordpress/stack 
? Select tag to run [monk-wordpress/stack] on: wp
✔ Starting the job: monk-wordpress/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% docker.io/library/mysql:latest wordpress
✔ [================================================] 100% docker.io/library/wordpress:latest wordpress
✔ [================================================] 100% docker.io/library/busybox:latest wordpress
✔ Checking/pulling images DONE
✔ Starting containers DONE
✔ Starting containers DONE
✔ Started monk-wordpress/stack

🔩 templates/local/monk-wordpress/stack
 └─🧊 Peer wordpress
    ├─🔩 templates/local/monk-wordpress/db
    │  └─📦 7f8edcacb4fffec125c095e6b3b7d946-local-monk-wordpress-db-mysql
    │     ├─🧩 docker.io/library/mysql:latest
    │     ├─💾 /var/lib/monkd/volumes/wordpress-mysql -> /var/lib/mysql
    │     └─🔌 open 34.146.77.2:3306 (0.0.0.0:3306) -> 3306
    ├─🔩 templates/local/monk-wordpress/busy
    │  └─📦 8deeb13b7974c7c9aa5205e62757f096-local-monk-wordpress-busy-test
    │     └─🧩 docker.io/library/busybox:latest
    └─🔩 templates/local/monk-wordpress/wordpress
       └─📦 cb0d77faf4127885233ee854c4104da4--wordpress-wordpress-wordpress
          ├─🧩 docker.io/library/wordpress:latest
          ├─💾 /var/lib/monkd/volumes/wordpress/web -> /var/www/html
          ├─🔌 open 34.146.77.2:8088 -> 80
          └─🔌 open 34.146.77.2:443 -> 443

💡 You can inspect and manage your above stack with these commands:
	monk logs (-f) monk-wordpress/stack - Inspect logs
	monk shell     monk-wordpress/stack - Connect to the container's shell
	monk do        monk-wordpress/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

If you have forwarded the domain to the server, you can access it directly from the domain name.

http://wordpress.burakhan.com

## Variables
The variables are in `stack.yml` file. You can quickly setup by editing the values here.

| Variable                     	| Description                               	|
|------------------------------	|-------------------------------------------	|
| mysql_database_user          	| Database username that wordpress will use 	|
| mysql_database_root_password 	| Database authorized user password         	|
| mysql_database_password      	| Database password that wordpress will use 	|
| server_name                  	| The domain name you want to run           	|
| mysql-image-tag              	| The mysql version you want to use         	|
| mysql_database_name          	| Database name that wordpress will use     	|
| wordpress_port          	      | Serve wordpress port     	|


##

## Stop, remove and clean up workloads and templates

```bash
monk purge -x monk-wordpress/stack monk-wordpress/db monk-wordpress/wordpress monk-wordpress/nginx
```

```
