Please refer to upstream for up-to-date README:

# [linuxserver/smokeping](https://github.com/linuxserver/docker-smokeping)

[Smokeping](https://oss.oetiker.ch/smokeping/) keeps track of your network latency. For a full example of what this application is capable of visit [UCDavis](http://smokeping.ucdavis.edu/cgi-bin/smokeping.fcgi).

[![smokeping](https://camo.githubusercontent.com/e0694ef783e3fd1d74e6776b28822ced01c7cc17/687474703a2f2f6f73732e6f6574696b65722e63682f736d6f6b6570696e672f696e632f736d6f6b6570696e672d6c6f676f2e706e67)](https://oss.oetiker.ch/smokeping/)

## Modifications

From the linuxserver.io build of this image, speedtest-cli capability has been added. Default Probe settings are 3600 seconds, but this can be modified with the "step" parameter in the /config/Probes file.

See further: [mad-ady/smokeping-speedtest](https://github.com/mad-ady/smokeping-speedtest)

## Troubleshooting

Make sure the following is in your /config/Probes file if you receive an error about missing probes:

```
+ speedtest
binary = /usr/bin/speedtest-cli
timeout = 300
step = 3600
offset = random
pings = 3

++ speedtest-download
measurement = download

++ speedtest-upload
measurement = upload
```

Read about Target file configuration here:

https://github.com/mad-ady/smokeping-speedtest/blob/master/README.md

The rest of this README is copied from upstream and may not be current.

## Supported Architectures

Our images support multiple architectures such as `x86-64`, `arm64` and `armhf`. We utilise the docker manifest for multi-platform awareness. More information is available from docker [here](https://github.com/docker/distribution/blob/master/docs/spec/manifest-v2-2.md#manifest-list) and our announcement [here](https://blog.linuxserver.io/2019/02/21/the-lsio-pipeline-project/).

Simply pulling `linuxserver/smokeping` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Tag |
| :----: | --- |
| x86-64 | amd64-latest |
| arm64 | arm64v8-latest |
| armhf | arm32v7-latest |


## Usage

Here are some example snippets to help you get started creating a container.

### docker

```
docker create \
  --name=smokeping \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -p 80:80 \
  -v </path/to/smokeping/config>:/config \
  -v </path/to/smokeping/data>:/data \
  --restart unless-stopped \
  linuxserver/smokeping
```


### docker-compose

Compatible with docker-compose v2 schemas.

```
---
version: "2"
services:
  smokeping:
    image: linuxserver/smokeping
    container_name: smokeping
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    volumes:
      - </path/to/smokeping/config>:/config
      - </path/to/smokeping/data>:/data
    ports:
      - 80:80
    restart: unless-stopped
```

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 80` | Allows HTTP access to the internal webserver. |
| `-e PUID=1000` | for UserID - see below for explanation |
| `-e PGID=1000` | for GroupID - see below for explanation |
| `-e TZ=Europe/London` | Specify a timezone to use EG Europe/London |
| `-v /config` | Configure the `Targets` file here |
| `-v /data` | Storage location for db and application data (graphs etc) |

## User / Group Identifiers

When using volumes (`-v` flags) permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id user` as below:

```
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```


&nbsp;
## Application Setup

- Once running the URL will be `http://<host-ip>/`.
- Basics are, edit the `Targets` file to ping the hosts you're interested in to match the format found there.
- Wait 10 minutes.



## Support Info

* Shell access whilst the container is running: `docker exec -it smokeping /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f smokeping`
* container version number
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' smokeping`
* image version number
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' linuxserver/smokeping`

## Updating Info

Most of our images are static, versioned, and require an image update and container recreation to update the app inside. With some exceptions (ie. nextcloud, plex), we do not recommend or support updating apps inside the container. Please consult the [Application Setup](#application-setup) section above to see if it is recommended for the image.

Below are the instructions for updating containers:

### Via Docker Run/Create
* Update the image: `docker pull linuxserver/smokeping`
* Stop the running container: `docker stop smokeping`
* Delete the container: `docker rm smokeping`
* Recreate a new container with the same docker create parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* Start the new container: `docker start smokeping`
* You can also remove the old dangling images: `docker image prune`

### Via Docker Compose
* Update all images: `docker-compose pull`
  * or update a single image: `docker-compose pull smokeping`
* Let compose update all containers as necessary: `docker-compose up -d`
  * or update a single container: `docker-compose up -d smokeping`
* You can also remove the old dangling images: `docker image prune`

### Via Watchtower auto-updater (especially useful if you don't remember the original parameters)
* Pull the latest image at its tag and replace it with the same env variables in one run:
  ```
  docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --run-once smokeping
  ```

**Note:** We do not endorse the use of Watchtower as a solution to automated updates of existing Docker containers. In fact we generally discourage automated updates. However, this is a useful tool for one-time manual updates of containers where you have forgotten the original parameters. In the long term, we highly recommend using Docker Compose.

* You can also remove the old dangling images: `docker image prune`

## Building locally

If you want to make local modifications to these images for development purposes or just to customize the logic:
```
git clone https://github.com/linuxserver/docker-smokeping.git
cd docker-smokeping
docker build \
  --no-cache \
  --pull \
  -t linuxserver/smokeping:latest .
```

The ARM variants can be built on x86_64 hardware using `multiarch/qemu-user-static`
```
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Once registered you can define the dockerfile to use with `-f Dockerfile.aarch64`.
