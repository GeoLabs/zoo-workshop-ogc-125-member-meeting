# zoo-workshop-ogc-125-member-meeting
ZOO-Project workshop to be held during the 125th OGC members meeting in Frascati

## Setup the ZOO-Project using Docker

````
export WS_DIR=~/zoo_ws_ogc
mkdir $WS_DIR
cd $WS_DIR
git clone https://github.com/ZOO-Project/ZOO-Project.git ZPGIT
cd ZPGIT
docker-compose up -d
````
Access your OGC API - Processes - Part 1: Core [landing page](http://localhost/ogc-api/).

## Secure access to specific end-points

First run a shell from the running container.

````
docker exec -it zpgit_zookernel_1 bash
````

From this shell, use the commands below to add an access restriction to the HelloPy execution end-point. The `sed` command is used to add the path properly to the list.

````
cat >> $WS_DIR/ZPGIT/docker/oas.cfg << EOF
[processes/HelloPy/execution]
length=1
pname=HelloPy
rel=http://www.opengis.net/def/rel/ogc/1.0/execute
method=post
secured=BasicAuth
title=execute a job
abstract=An execute endpoint.
tags=ExecuteEndpoint
tags_description=
schema=http://schemas.opengis.net/ogcapi/processes/part1/1.0/openapi/responses/ExecuteSync.yaml
parameters=/components/parameters/oas-header1
ecode=400,404,500
eschema=http://schemas.opengis.net/ogcapi/processes/part1/1.0/openapi/responses/ExecuteAsync.yaml

[osecurity]
name=BasicAuth
type=http
scheme=basic
realm=Secured section
charset=utf-8
passwd=/tmp/htpasswords

[filter_in]
path=/usr/lib/cgi-bin
service=securityIn

[filter_out]
path=/usr/lib/cgi-bin
service=securityOut
EOF

sed "s#/processes/OTB.BandMath/execution,#/processes/OTB.BandMath/execution,/processes/HelloPy/execution,#g" -i $WS_DIR/ZPGIT/docker/oas.cfg
````

Here, we use the default value provided in the official documentation. Nevertheless, the filter_out is only to illustrate the fact that somethign can be run be fore returning the result to the client application.

## Create a password file

As before, run a shell from the running container.

````
docker exec -it zpgit_zookernel_1 bash
````

From this shell, run the following command.

````
htpasswd -c -b /tmp/passwords test test
````

## Using Keycloack to authenticate

## Future steps

