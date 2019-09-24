# Sample 3tier app
This repo contains code for a Node.js multi-tier application.

The application overview is as follows

```
web <=> api <=> db
```

The folders `web` and `api` respectively describe how to install and run each app.

## Bringing up the development environment
The development environment for this application can be brought up with docker compose. In order run this environment it's necessary to checkout [web app](https://github.com/camharris/node-3tier-app-we) into the parent directory of this repo.
```
$ pwd
/home/cam/projects/toptal
$ tree -L 1
.
├── node-3tier-app
└── node-3tier-app-web

2 directories, 0 files
```
Changes can be made to either the web or api tier of this application by editing either the web repo or this one. When ready to run this application locally
do the following in the root of this repo:
```
docker-compose build
docker-compose up
```


## Infrastructure and CI/CD pipeline 
For more information on building the infrastructure and CI/CD pipeline view the [Infrastucture installation](INSTALL.md)

## Presentation outlining architecture
[Google Presentation](https://docs.google.com/presentation/d/18GmstDgqSejoV_8-sTRUeDI48ayv_wuw01Vg8FAetW0/edit?usp=sharing)

## Links to my relevant github repos:
This project was seperated into two seperate repositories and moved to github:

[Primary repo including api/db tier](https://github.com/camharris/node-3tier-app)

[web tier](https://github.com/camharris/node-3tier-app-web)
