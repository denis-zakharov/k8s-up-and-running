# Dockerfile

FROM base image
LABEL key=value # docker inspect
USER root
RUN cmd
ADD local_or_url image_dst
WORKDIR new_wd_for_a_build
CMD ["myprog", "-args"]

# Build

## Single stage

docker build -t proj/name:tag dockerfile_dir

## Export

docker export image_id -o app.tar

## Inspect

docker image inspect id

## Troubleshooting from the last stage

docker run -it --rm container_id /bin/bash

# Commands

docker run -d -p host_port:container_port proj/name:tag -e ENV_VAR=VALUE

docker login # .docker/config.json

docker tag proj/name:tag new_proj/name:tag

docker image ls

docker container ps

docker stop
