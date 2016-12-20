# Nextcloud

A safe home for all your data. Access & share your files, calendars, contacts, mail & more from any device, on your terms.

# Contents
This is a collection of docker-compose examples for installing nextcloud with docker containers.

Each subfolder contains a `docker-compose.yml` file and an `install` script. The install script creates mandatory configurations for the containers to run properly.

The compose files and the install script are controlled with environment variables from an `.env` file that has to be created first. You can find the environment variables in the .env-example file of each folder.

## minimal
The minimal example just creates a nextcloud instance, a simple nginx webserver and a mariaDB database. It exposes the nextcloud installation to port 80 of your host. This example is insecure as any communication to your server is unencrypted.
Just use this image as example or for local testing.
The data is stored in a subfolder called `nextcloud`. There you can find `db`,`apps`, `config` and `data`.

## reverse proxy with letsencrypt
This example provides a full nextcloud installation that can be run as is. It uses the [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/) and the [jrcs/letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/) to automatically create letsencrypt certificates for you domains and redirects any request to https.
The reverse proxy resolves your containers to subdomains, so make sure to use a subdomain such as `cloud.example.com` as domain name.
The reverse proxy configuration is stored in a subfolder called `proxy`. 

## redis
The redis example builds on the reverse proxy with letsencrypt and adds file locking in a redis container. For local caching uAPC will be used.

## collabora
The collabora example builds on redis and adds the collabora [collabora/code](https://hub.docker.com/r/collabora/code/) container for online document editing.
The collabora container will use a different subdomain than you nextcloud installation, for example `office.example.com`.
To activate collabora you have to install the collabora-connector app from the productivity tab in the appstore (make sure to activate experimental apps) and enter your collabora subdomain name in the settings (without any port).

# Installation
First you have to create a `.env` file next to the `docker-compose` file you would like to use. Use the .env-example file to see which variables are needed.

```bash
cp .env-example .env
nano .env-example
```

When you have your .env file run the install script.

```bash
./install
```

If you're using an example with the reverse proxy (reverseproxy, redis, collabora) then you have to create a docker network.
```bash
docker network create -d bridge nginx-proxy
```

After that you can start the containers. Depending on the example your using it can take a few moments for all containers to start. 

```bash
docker-compose up -d
```

Once started, you can access your nextcloud. You'll arrive at the configuration wizard.
At the `Database Setup` step, please enter the following:

  -  Database Server: `db`
  -  Login: `nextcloud`
  -  Password: `<your password>`
  -  Database Name: `nextcloud`

And leave the rest as default.

# Update
The nextcloud server files are stored in a docker volume and will be persistant even if the container stopped and restartet. 
To get recent container upgrades you have two options. The first is using the upgrade script provided by nextcloud. Documentation can be found [here](). 

But if the container is rebuild from the image a new docker volume will be created and the changes made by the upgrade script are lost. You will not only fall back to the version specified in the image from the compose file, but nextcloud will also refuse to work, because you database and files are on a newer version then the server files and downgrading is not supported.

The better way to upgrade when working with docker containers is going the "docker way". If you use official images you have to wait until the image of new version is available. You can simply pull it by:

```bash
docker-compose pull
```

Before you start your new version you have to make sure, that the old server files are deleted from the volume. To do that you can use 

```bash
docker-compose down -v
```

Now have to use the `up` command again. 
```bash
docker-compose up -d
```

When you access your nextcloud with the browser you will be guided through the update process.

# Delete / Reset your configuration
If you're testing you might want to delete all the data and start from scratch. For that you stop and delete the docker containers und delete the subfolders created with the installation. Beware that you must have superuser rights to delete the subfolders created by docker.

```bash
docker-compose down 
sudo rm -r nextcloud/
sudo rm -r proxy/
```

# Contribute

Pull requests are very welcome!

We'd love to hear your feedback and suggestions in the [issue tracker](https://github.com/SnowMB/nextcloud/issues).
