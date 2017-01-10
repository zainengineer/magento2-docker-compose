This docker-compose.yml file is provided by Mage Inferno

## Custom Notes
* Following changes are so you use do local modifications to the file
  some section might not be needed but wrote it down to produce original functionality
* uncomment `#- /var/www/html` and comment the next line in `docker-compose.yml`
* this will create magento project in docker volume
* Run `docker-compose run`
* Test the access by `http://m2.localhost:8000/`
* `Control-C` to stop docker
* restore original `docker-compose.yml`
* Run `sudo docker-compose run`
* Run in a separate shell in this directory `sudo chown zain:zain share_code/ -R`

## Docker Hub

View our Docker Hub images at [https://hub.docker.com/u/mageinferno/](https://hub.docker.com/u/mageinferno/)

## Usage

This file is provided as an example development environment using Mage Inferno Magento 2 Docker Images.

## Composer Setup

### Authentication

Uncomment the composer line from `appdata` to mount a `.composer` directory to the `www-data` user home directory. Please first setup Magento Marketplace authentication (details at <a href="http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html" target="_blank">http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html</a>).

Place your auth token at `~/.composer/auth.json` with the following contents, like so:

```
{
    "http-basic": {
        "repo.magento.com": {
            "username": "MAGENTO_PUBLIC_KEY",
            "password": "MAGENTO_PRIVATE_KEY"
        }
    }
}
```

Then, just set `M2SETUP_USE_ARCHIVE` to `false` in your docker-compose.yml file. 

### Magento Enterprise 

You can install Magento Enterprise via Composer by setting `M2SETUP_USE_COMPOSER_ENTERPRISE=true` in your docker-compose.yml file.

## Composer-less, No-Auth Setup

If you don't want to use Composer or setup the auth keys above, no worries. Mage Inferno install script uses Nexcess' hosted Magento archives for a Composer-less install process. Just set the `M2SETUP_USE_ARCHIVE` environment variable to `true` when running setup.

## Running Setup

Using the above `docker-compose.yml` file, all you need to do is run one line to install Magento 2:

```
docker-compose run --rm setup
```

You may modify any environment variables depending on your requirements.

## Data Volumes

Your Magento source data is persistently stored within Docker data volumes. For local development, we advise copying the entire contents of the `appdata` data volume to your local machine (after setup is complete of course). Since you shouldn't be modifying any of these files, this is just to bring the fully copy of the site back to your host:

```
docker cp CONTAINERID:/var/www/html ./
```

Then, just uncomment the `./html/app/code:/var/www/html/app/code` and `./html/app/code:/var/www/html/app/code` lines within your docker-compose.yml file (appdata > volumes). This mounts your local `app/code` and `app/design` directories to the Docker data volume. Then, just restart your containers:

```
docker-compose up -d app
```

Any edits to these directories will correctly sync with your Docker volume.

## Running Magento CLI tool

We've setup scripts to aid in the running of Magento CLI tool with the correct permissions. To run the command line tool, you would connect as any other Docker Compose application would:

```
docker-compose exec phpfpm ./bin/magento
```

or with straight Docker command:
```
docker exec NAME_OF_PHPFPM_CONTAINER ./bin/magento
```

You can easily set these up as aliases inside your `~/.bash_profile` file (or a similar script) as so:

```
alias magento='docker-compose exec phpfpm ./bin/magento'
```
This will allow you to clear the cache by running the following command right in terminal:

```
magento cache:flush
```

## Docker Compose Override

You can copy `docker-compose.override.yml.dist` to `docker-compose.override.yml` and adjust environment variables, volume mounts etc in the `docker-compose.override.yml` file to avoid losing local configuration changes when you pull changes to this repository. 

Docker Compose will automatically read any of the values you define in the file. See [this link](https://docs.docker.com/compose/extends/#/understanding-multiple-compose-files) for more information about the override file. 
