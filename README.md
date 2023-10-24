# Docker workspace for PHP web applications (PHP+Nginx+MariaDB+Manticore)

- PHP 8.1.18
- Nginx:latest
- MariaDB 10.11.5
- Manticore:latest

**1**. Copy **.env-example** to **.env**
```shell script
cp .env-example .env
```

**2**. Place your project into **./projects** folder

**3**. Add nginx configurations for your project in **./nginx/conf.d/vhost.conf**

**4**. Create containers and run:
```shell script
docker-compose build && docker-compose up -d
```

##Tips:

###"extra_hosts"
You will probably want to allow access between applications (containers), so you need to add values for the **"extra_hosts"** options in **docker-compose.yaml** for the **php** container.

```
  ...  
  php:  
  ...
    extra_hosts:
      - 'host.docker.internal:host-gateway'
      - 'project-1.local:IP_HOST_MACHINE'
      - 'project-2.local:IP_HOST_MACHINE'
  ...
```

**IP_HOST_MACHINE** — The IP address of your machine in the docker subnetwork.

For **macOS**, you can find out your IP on the docker subnet using the following command:

```shell script
docker run -it alpine ping docker.for.mac.localhost
```
 
###SSH keys
If you need the SSH key in the **php** container then you can copy your current one to the ./ssh directory or generate a new one:

```shell script
ssh-keygen -f ./.ssh/id_rsa -t rsa -b 2048 -C "your-name@example.com"
```

**NOTE!**<br>
If you copied your previously created ssh key into the .ssh folder, make sure the id_rsa file has permissions **700 (-rwx------@)**.
