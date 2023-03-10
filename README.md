# Getting started

This folder contains all the things needed to setup a dev (and production?) environment for Greenwich.

It assumes the following structure, but this can be amended to use images from dockerhub or other services.

```
/greenwich-outpost-platform
/greenwich-outpost-platform/greenwich-outpost
/greenwich-outpost-platform/outpost
/greenwich-outpost-platform/outpost-api-service
/greenwich-outpost-platform/scout-x
```

Create `.env` file from `sample.env`.

You can send .env files through docker-compose or use `.env.outpost`, `.env.outpost-api` and `.env.scout` files.

```sh
# build the images
docker-compose -f docker-compose.development.yml build

# start the images
docker-compose -f docker-compose.development.yml up -d

# stop the images
docker-compose -f docker-compose.development.yml stop

```

# initial setup

```sh
# run migrations on the outpost db
docker compose -f docker-compose.development.yml exec outpost rails db:migrate

# seed the db if needed (IMPORT_SAMPLE_DATA will add dummy data) admin user is always created
docker compose -f docker-compose.development.yml exec -e IMPORT_SAMPLE_DATA=true outpost rails db:seed
docker compose -f docker-compose.development.yml exec outpost rails db:seed

# push data from outpost db to outpost-api db
docker compose -f docker-compose.development.yml exec outpost rake build_public_index

# setup API db indices
docker compose -f docker-compose.development.yml exec outpost-api npm run prepare-indices
```

# Build images

```sh
docker-compose -f docker-compose.development.yml build
```

# Start images

```sh
docker-compose -f docker-compose.development.yml up -d
```

# Access shell

```sh
docker-compose -f docker-compose.development.yml exec outpost /bin/ash;
docker-compose -f docker-compose.development.yml exec outpost-api /bin/ash;
docker-compose -f docker-compose.development.yml exec scout /bin/ash;
```

# Stop containers

```sh
docker-compose -f docker-compose.development.yml stop
```

# Reset DB

In case of emergency!

```sh
docker compose -f docker-compose.development.yml exec outpost rails db:reset db:migrate
```

# Importing data

```sh
docker compose -f docker-compose.development.yml exec outpost rake convert:openobjects
docker compose -f docker-compose.development.yml exec -e IMPORT_HIERARCHICAL_TAXONOMIES=true outpost rake import:services
docker compose -f docker-compose.development.yml exec outpost rake import:custom_fields
docker compose -f docker-compose.development.yml exec outpost rake import:services
```
