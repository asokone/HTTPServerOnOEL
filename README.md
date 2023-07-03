
# Deploying hhpd server using Dockerfile

Deploying Python SimpleHTTPServer (HTTPD server) on a Docker container using Dockerfile

## Authors

- [asokone@thecloudedu.com](https://www.github.com/asokone)


## What is in this image?

Python Httpd is a lightweight HyperText Transfer Protocol (HTTP) server program. It is designed to be run as a standalone daemon process. I am using it the create my first Webpage.

## Dockerfile -  images based on Oracle Linux 8.2

FROM oraclelinux:8.2


RUN dnf install -y httpd
RUN dnf install -y sudo
RUN dnf install -y which
RUN dnf install -y perl


WORKDIR /var/www/cgi-bin
COPY counter.pl counter.pl
COPY counter.txt counter.txt
RUN chown apache:apache counter.pl counter.txt

WORKDIR /var/www/html
COPY AndialySokone.jpg AndialySokone.jpg
COPY index.html index.html
RUN chown apache:apache index.html AndialySokone.jpg

LABEL maintainer="asokone@thecloudedu.com" \
version="1.0.1"

EXPOSE 80

ENTRYPOINT /usr/sbin/httpd -D FOREGROUND

## Build

$ cd /home/asokone/kubernetes/Kube_Fundamentals/github

$ ls

AndialySokone.jpg  application.py  counter.pl  counter.txt  Dockerfile  Dockerfile.Alpine  Dockerfile.oraclelunix8.2  index.html

$ docker image build -t myweb .

[+] Building 754.1s (19/19) FINISHED
 => [internal] load .dockerignore                                                                                           
 => => transferring context: 2B                                                                                             
 => [internal] load build definition from Dockerfile                                                                        
 => => transferring dockerfile: 851B                                                                                        
 => [internal] load metadata for docker.io/library/oraclelinux:8.2                                                          
 => [auth] library/oraclelinux:pull token for registry-1.docker.io                                                          
 => CACHED [ 1/13] FROM docker.io/library/oraclelinux:8.2@sha256:528f50bfc64d39a72b2a3d02147cc88af35ec15a036e5c3ecc162baaede
 => [internal] load build context                                                                                           
 => => transferring context: 88.35kB                                                                                        
 => [ 2/13] RUN dnf install -y httpd                                                                                        
 => [ 3/13] RUN dnf install -y sudo                                                                                         
 => [ 4/13] RUN dnf install -y which                                                                                        
 => [ 5/13] RUN dnf install -y perl                                                                                         
 => [ 6/13] WORKDIR /var/www/cgi-bin                                                                                        
 => [ 7/13] COPY counter.pl counter.pl                                                                                      
 => [ 8/13] COPY counter.txt counter.txt                                                                                    
 => [ 9/13] RUN chown apache:apache counter.pl counter.txt                                                                  
 => [10/13] WORKDIR /var/www/html                                                                                           
 => [11/13] COPY AndialySokone.jpg AndialySokone.jpg                                                                        
 => [12/13] COPY index.html index.html                                                                                      
 => [13/13] RUN chown apache:apache index.html AndialySokone.jpg                                                            
 => exporting to image                                                                                                      
 => => exporting layers                                                                                                     
 => => writing image sha256:a1042869ca14e8ada126d6860a6e8cd6e79802f06e9290be97391c07cf73a507                                
 => => naming to docker.io/library/myweb                                                                                    

## Deployment

$ docker images | egrep "REPOSITORY|myweb"

REPOSITORY              TAG         IMAGE ID        CREATED             SIZE

myweb                   latest      68b37e3ce0a4    43 seconds ago      71.3MB

Let us check if the port 8080 is in use in this localhost

$ curl localhost:8080/

curl: (7) Failed to connect to localhost port 8080: Connection refused


The port 8080 is not in use let us proceed and create our container name myfristweb using the image we just create myweb
 
$ docker run --name myfirstweb -dit -p 8080:80 myweb

fbfa6491c341a2b08f212249ea3d65ca21cdc4b69635ca52ab9a8e84268736e7

$ docker container ps -a | grep myweb

fbfa6491c341   myweb                    "/bin/sh -c 'python …"   3 minutes ago   Up 2 minutes              0.0.0.0:8080->80/tcp, :::8080->80/tcp   myfirstweb


## Docker Hub Login

We supposed that you have created already an account at https://hub.docker.com/

$ docker login

Authenticating with existing credentials...

WARNING! Your password will be stored unencrypted in /root/.docker/config.json.

Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

## Accessing the deployment

$ curl http://localhost:8080/

<html>
        <head>
                <title>This is Andialy's First Webpage</title>
        </head>
        <body>
                <center>
<hr>
               <img src="/AndialySokone.jpg" width="200" height="256">
                        <pre>
                        <tt>
<hr>
Hello!
I am <b> Andialy Sokone </b>
This is my first Web Site built on docker container using perl as CGI counter
See how easy docker is !!
<hr>
                        </tt>
                        </pre>
                </center>
        </body>
</html>

## Cleaning up

$ docker container stop  myfirstweb

myfirstweb

$ docker container rm  myfirstweb

myfirstweb

$ docker image rm myweb

## Verifying if things are cleaned

$ docker container ps -a | grep myfirstweb
