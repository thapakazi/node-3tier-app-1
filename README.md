# Sample 3tier app
This repo contains code for a Node.js multi-tier application.

The application overview is as follows

```
web <=> api <=> db
```

The folders `web` and `api` respectively describe how to install and run each app.

## Bringing up the development environment
The development environment for this application can be brought up with docker compose.
Changes can be made to either the web or api tier of this application, when ready to locally 
do the following in the root of this repo:
```
docker-compose build
docker-compose up
```

## Infrastructure and CI/CD pipeline 
For more information on building th infrastructure and CI/CD project view the [Infrastucture installation](INSTALL.md)
