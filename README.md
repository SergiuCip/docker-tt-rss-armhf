
[hub]: https://hub.docker.com/r/lsioarmhf/tt-rss/


# sergiucc/tt-rss

[Tiny Tiny RSS][appurl] is an open source web-based news feed (RSS/Atom) reader and aggregator, designed to allow you to read news from any location, while feeling as close to a real desktop application as possible.


## Usage

```
docker create \
--name=tt-rss \
-v /etc/localtime:/etc/localtime:ro \
-v <path to data>:/config \
-e PGID=<gid> -e PUID=<uid> \
-e TZ=<timezone> \
-p 80:80 \
sergiucc/tt-rss
```

## Parameters

`The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side. 
For example with a port -p external:internal - what this shows is the port mapping from internal to external of the container.
So -p 8080:80 would expose port 80 from inside the container to be accessible from the host's IP on port 8080
http://192.168.x.x:8080 would show you what's running INSIDE the container on port 80.`


* `-p 80` - webui port *see note below*
* `-v /etc/localtime` for timesync - *optional* *omit if using TZ variable*
* `-v /config` - where tt-rss should store it's config files
* `-e PGID` for GroupID - see below for explanation
* `-e PUID` for UserID - see below for explanation
* `-e TZ` for setting timezone information, eg Europe/London 

It is based on alpine linux with s6 overlay, for shell access whilst the container is running do `docker exec -it tt-rss /bin/bash`.

### User / Group Identifiers

Sometimes when using data volumes (`-v` flags) permissions issues can arise between the host OS and the container. We avoid this issue by allowing you to specify the user `PUID` and group `PGID`. Ensure the data volume directory on the host is owned by the same user you specify and it will "just work" ™.

In this instance `PUID=1001` and `PGID=1001`. To find yours use `id user` as below:

```
  $ id <dockeruser>
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```

## Setting up the application 
`IMPORTANT... THIS IS THE ARMHF VERSION`

You must create a user and database for tt-rss to use in a mysql/mariadb or postgresql server. In the setup page for database, use the ip address rather than hostname...

A basic nginx configuration file can be found in /config/nginx/site-confs , edit the file to enable ssl (port 443 by default), set servername etc.. Self-signed keys are generated the first time you run the container and can be found in /config/keys , if needed, you can replace them with your own.

The site files are in /config/www/tt-rss , you can find config files and themes folder there. Email and other settings are in the config.php file.

## Info

* To monitor the logs of the container in realtime `docker logs -f tt-rss`.

* container version number 

`docker inspect -f '{{ index .Config.Labels "build_version" }}' tt-rss`

* image version number

`docker inspect -f '{{ index .Config.Labels "build_version" }}' lsioarmhf/tt-rss`


## Versions

+ **31.12.19:** Forked project and removed deprecation notices
+ **21.08.18:** Rebase to alpine 3.8.
+ **08.01.18:** Rebase to alpine 3.7.
+ **19.07.17:** Use updated [repository](https://git.tt-rss.org/git/tt-rss) for initial install.
+ **29.05.17:** Rebase to alpine 3.6.
+ **03.05.17:** Update to php 7.1x.
+ **24.02.17:** Initial Release.
