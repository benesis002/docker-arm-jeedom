#Fork from [Cquad/jeedom](https://github.com/Cquad/jeedom) scripts to run jeedom into container on arm computers

It only consists in changing FROM instructions in Dockerfiles in order to get an ARM Debian:jessie version of the OS.

Version used :
- hareemca123/arm-mysql:latest
- ioft/armhf-debian:jessie

First, start a mysql container:

```
docker run -d --name jeedom-mysql -e MYSQL_ROOT_PASSWORD=password hareemca123/arm-mysql:latest
```

Clone everything here and cd into the jeedom-data directory. Create the data container:

```
docker build -t arm-jeedom-data .
```

Launch the data container. This data container will install jeedom on mysql DB :

```
docker run --name jeedom-data --link jeedom-mysql:mysql arm-jeedom-data
```

cd into your jeedom-web directory and create the web container:

```
docker build -t arm-jeedom-web .
```

And finally the main container, web front :

```
docker run -d --name jeedom-web --volumes-from jeedom-data --link jeedom-mysql:mysql -p 80:80 -p 8070:8070 arm-jeedom-web
```

For more detail on how to run, see [Cquad/jeedom](https://github.com/Cquad/jeedom) repo.

++ polo
