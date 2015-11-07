#Fork from [Cquad/jeedom](https://github.com/Cquad/jeedom) scripts to run jeedom into container on arm computers


It only consists in changing FROM instructions in Dockerfiles in order to get an ARM Debian:jessie version of the OS.

Version used :
- armhfbuild/mysql:5.6
- armhfbuild/debian:jessie
- [appellemoipolo/docker-arm-samba](https://github.com/appellemoipolo/docker-arm-samba)

First, start a mysql container and define MYSQL_USER, MYSQL_PASSWORD, MYSQL_ROOT_PASSWORD and MYSQL_DATABASE environment variables:

```
docker run -d --name jeedom-mysql -p 3306:3306 -e MYSQL_USER=jeedom -e MYSQL_PASSWORD=jeedom -e MYSQL_ROOT_PASSWORD=jeedom -e MYSQL_DATABASE=jeedom armhfbuild/mysql:5.6
```

Clone everything here and cd into the jeedom-data directory. This container fits with my personnal needs in terms of volumes. I added sonos tts directories prepared for a samba share. Create the data container:

```
docker build -t arm-jeedom-data .
```

Launch the data container. This data container will install jeedom on mysql DB with the variables you defined previously in JEEDOM_DB_USER, JEEDOM_DB_PASSWORD and JEEDOM_DB_NAME:

```
run --name jeedom-data -e JEEDOM_DB_USER=jeedom -e JEEDOM_DB_PASSWORD=jeedom -e JEEDOM_DB_NAME=jeedom --link jeedom-mysql:mysql arm-jeedom-data
```

cd into your jeedom-web directory and create the web container:

```
docker build -t arm-jeedom-web .
```

And finally the main container, web front:

```
docker run -d -p 80:80 -p 8070:8070 -p 8083:8083 -p 9001:9001 -p 443:443 -p 17100:17100 --name jeedom-web --volumes-from jeedom-data --link jeedom-mysql:mysql arm-jessie-jeedom-web
```

Finally, to use the sonos plugin with tts :

```
docker run -d --name jeedom-samba-sonos-tts -p 139:139 -p 445:445 --volumes-from jeedom-web arm-samba -s "sonos-tts;/tmp/sonos-tts"
```

#Depreciated#

An other (bad) possibility is to run the all-in-one container:

```
docker run -d -p 80:80 -p 22:22 -p 8070:8070 -p 9001:9001 -name jeedom-all-in-one arm-jeedom-all-in-one
````

Or with --net=host to share all ports with host without using docker nat system.

```
docker run -d (-p 80:80 -p 222:22 -p 8070:8070 -p 9001:9001) --net=host -name jeedom-all-in-one arm-jeedom-all-in-one
```


For more detail on how to run, see [Cquad/jeedom](https://github.com/Cquad/jeedom) repo.

++ polo
