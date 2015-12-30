# Learn App Development with Docker

https://www.udemy.com/learn-app-development-with-docker

6 Section Course
- 1 intro
- 2 basic docker concepts
- 3 developing using Docker
- 4 advanced concepts
- 5 security
- 6 summary

So far seems dry - in section 2.

## Hypervisors vs Containers

A hypervisor slices up hardware into virtual machines each running a full OS and apps. Containers put slices on top of an OS so that each container is isolated but resources and libraries are shared. The app runs in the container.

### Containers have the advantages:

- light weight - the OS, libs etc is not duplicated
- portable - smaller images (see above)
- more efficient - less layers (see above)
- containers use less storage space - small deltas for each layer
- containers can boot and be application ready in ~500ms vs 20s ++ for virtulization
- APIs - containers have built in standardized APIs for "cloud ochestration" -- automation -- will find a good definition. 


### Docker

- framework for container virtualization
- shipping containers - standardized
- docker adds a wrapper with a tool chain
- developers care about apps, operations cares about servers

## Section 2

### Installing docker

### On Linux

Installing on my ubuntu nuc for development and playing.

```wget -qO- https://get.docker.com/ | sh``` -- latest version - there is a package in wily - docker.io version 1.6.2 currently - better to get latest version from docker.com directly?

```sudo usermod -aG docker ali``` -- adds my user to the docker group

From docker directly - was 1.9.2 - preferred, is the overhead of manually maintaining versions better - probably I'll just script together with atom and the other 2 or 3 things I do this with.

### Windows and Mac

https://docs.docker.com/machine/migrate-to-machine/

Looks like boot2docker has been replaced by docker-machine.

## Hello World!

```docker run busybox echo Hello Docker``` - pulled down an image and worked.

So - looked for "busybox" in the local docker repository, then executed echo in the container's shell. Refered to as the "shell hook".

### Detail

So the "busybox" container image came from `https://registry.hub.docker.com/_/busybox`

`run busybox` creates a container from the image

Basic commands

`docker run` - creates and starts a container in one operation
`docker rm` - deletes a container
`docker kill` - sends SIGKILL to a container
`docker ps` - shows running containers
`docker ps -a` - shows running and stopped containers
`docker inspect` - detail about a container


```
ali@pluto:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS                     PORTS               NAMES
e8c3a0a00f32        busybox             "echo Hello Docker"   9 minutes ago       Exited (0) 9 minutes ago                       sad_austin
ali@pluto:~$ docker inspect sad_austin
... lots of info - including id ip etc
```

"busybox" - only 2.5mb - nice - so small, such wow.

Containers are free to store in the registry - if public. Paid required for a private repository.

```
ali@pluto:~$ docker rm e8c3a0a00f32
e8c3a0a00f32
ali@pluto:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

more: https://docs.docker.com/reference/commandline/


### Images

Common cmds

- `docker images` - show all images
- `docker build` - creates an image from Dockerfule
- `docker commit` - creates an image from container
- `docker rmi` - removes an image
- `docker save` - saves an image to a tar ball
- `docker import` - creates and image from a tarball
- `docker history` - shows the image history
- `docker tag` - tags an image to a name (local or registry)
Examples:

```
ali@pluto:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
busybox             latest              ac6a7980c6c2        2 weeks ago         1.113 MB
ali@pluto:~$ docker history busybox
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
ac6a7980c6c2        2 weeks ago         /bin/sh -c #(nop) CMD ["sh"]                    0 B                 
c00ef186408b        2 weeks ago         /bin/sh -c #(nop) ADD file:c295b0748bf05d4527   1.113 MB            
ali@pluto:~$ docker rmi ac6a7980c6c2
Untagged: busybox:latest
Deleted: ac6a7980c6c2fb4d29e406efb4f9784b3c67e161eb68a97ffb428d07e3e97693
Deleted: c00ef186408b85d9657e8241f53ccd1e7071f03b3d4b38863b2cdae88845b587
```

Note: phusion image is a "bridge" between container and VM concepts

`docker run --rm -t -i phusion/baseimage:0.9.16 /sbin/my_init -- bash -l`

Seems to be ~100mb.

`-t -i` - mean that the session is interactive - -i STDIN -t tty
` -- bash -l ` - starts the sevices and gives a terminal

Note: more at http://phusion.github.io/baseimage-docker/

### Typical Workflow

```
ali@pluto:~$ docker run --rm -t -i phusion/baseimage:0.9.16 /sbin/my_init -- bash -l
Unable to find image 'phusion/baseimage:0.9.16' locally
0.9.16: Pulling from phusion/baseimage
d634beec75db: Pull complete 
c134c5aad354: Pull complete 
7f261c8aa237: Pull complete 
d42e85b4cfc7: Pull complete 
819361702d1c: Pull complete 
b57f03987c1a: Pull complete 
b709df3b48a5: Pull complete 
a9d433e72f9f: Pull complete 
e409241cf856: Pull complete 
5a306358fa0c: Pull complete 
Digest: sha256:f55d261778d83def4a60d38939511cbed0268fcb91383c9e3edf2877ced289d9
Status: Downloaded newer image for phusion/baseimage:0.9.16
*** Running /etc/my_init.d/00_regen_ssh_host_keys.sh...
*** Running /etc/rc.local...
*** Booting runit daemon...
*** Runit started as PID 8
*** Running bash -l...
root@e8b4eeedd302:/# Dec 28 17:55:19 e8b4eeedd302 syslog-ng[14]: syslog-ng starting up; version='3.5.3'
Dec 28 18:17:01 e8b4eeedd302 /USR/SBIN/CRON[32]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
root@e8b4eeedd302:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@e8b4eeedd302:/# ps
  PID TTY          TIME CMD
    1 ?        00:00:00 my_init
    8 ?        00:00:00 runsvdir
    9 ?        00:00:00 bash
   35 ?        00:00:00 ps
root@e8b4eeedd302:/# exit
logout
*** bash exited with status 0.
*** Shutting down runit daemon (PID 8)...
*** Killing all processes...
ali@pluto:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```


Created an account at dockerhub.com - aliwatters

Renamed the docker image and now pushing.

```
ali@pluto:~$ docker tag 414ca7d4685d aliwatters/phusion-base-with-vim:1.0.0
ali@pluto:~$ docker images
REPOSITORY                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
phusion-base-with-vim              latest              414ca7d4685d        3 hours ago         301.3 MB
aliwatters/phusion-base-with-vim   1.0.0               414ca7d4685d        3 hours ago         301.3 MB
phusion/baseimage                  0.9.16              5a306358fa0c        11 months ago       279.7 MB
ali@pluto:~$ docker login
Username: aliwatters
Password: 
Email: ali.watters@gmail.com
WARNING: login credentials saved in /home/ali/.docker/config.json
Login Succeeded
ali@pluto:~$ docker push aliwatters/phusion-base-with-vim
The push refers to a repository [docker.io/aliwatters/phusion-base-with-vim] (len: 1)
414ca7d4685d: Pushed 
5a306358fa0c: Pushed 
e409241cf856: Pushed 
a9d433e72f9f: Pushed 
d42e85b4cfc7: Pushed 
7f261c8aa237: Pushed 
c134c5aad354: Pushed 
1.0.0: digest: sha256:035e3f1aaeb12596d94694a823bbcb64d2a68f404efb46e22ac125da561bf564 size: 21302
```

We could pull that image as needed on any servers with the command

```
docker oull aliwatters/phusion-base-with-vim
```


## Section 3 - Developing with Docker

### Docker files

`docker build -t="aliwatters/nodejsdev:1.0.0" .`

Read from the current directory (.) expecting a dockerfile there, and tag with the -t.

Three stages in a dockerfile.

1 - FROM 
2 - RUN
3 - CMD


Each of the commands is an instruction - chained together with "blah && \" 

Additional options: 

MAINTAINER dockerhub username
WORKDIR /dir
EXPOSE port

Note: 1 dockerfile per directory!
Note: complex with boot2docker! - easier on a linux machine.

```
mkdir project
vi dockerfile
docker build -t"blah" .
```

Will give us a docker image.

### Writing dockerfiles

https://docs.docker.com/engine/articles/dockerfile_best-practices/

Notes: https://hub.docker.com/_/debian/ - recommended as base image :)

To install things:

```
RUN apt-get update && apt-get install -y \
        package-bar \
        package-baz \
        package-foo
```

Must be chained as cache means outdated packages could be installed.

Midway through section 3 and it got complex - rather than pull 500mb java images and mess around with that crappy stuff - I decided to see about building out a helloworld Dockerfile with a go-app

Ended up being kind of awesome.

1) I don't need a base image if I compile my Go app - yep - I can just use the scratch baseimage. This means it's only 6mb!

2) use the -p command I can map multiple ports on my host into the same port on the containers

```
ali@pluto:~/docker/go-app$ docker run -d -p 8080:8080 aliwatters/go-app:1.0.0
f0b69893be84d906511446963162da5979f44505a57f8b4b6d6e15f838297a0a
ali@pluto:~/docker/go-app$ docker run -d -p 8081:8080 aliwatters/go-app:1.0.0
838f60a0745e7025705b06d21684807bfb9f7dabd222834e923c0f4e0346bf0c
```

like so.

3) the dockerfile is TINY

```
FROM scratch
ADD main /
CMD ["/main"]
```

copies the binary over, runs it and that's it.

See the go-app dir for the implementation

4) Compiling GO - make sure it's set for the same architecture - docker is going to be running on linux amd64 (might be exceptions - but for me always true)

```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -a -installsuffix cgo -o main .
```

TODO: check up on the CGO tag - I'm not sure if it's needed in go 1.5

Details on this approach originally found at: https://blog.codeship.com/building-minimal-docker-containers-for-go-applications/

Note: if the go app is going to DO ssl (eg fetching from https) - I'll need to add in certs from the host

```
FROM scratch
ADD ca-certificates.crt /etc/ssl/certs/
ADD main /
CMD ["/main"]
```

But - if I have nginx or haproxy handling ssl termination - won't need that for serving.  Once I get better at using official images - and attaching persistant storage - I'll write a go rest api app, something minimal that talks to a mongodb container.


--name : using this flag I can name my containers...

```
docker run --name goapp3 -d -p 8082:8080 aliwatters/go-app:1.0.0
```


Note: because I have no OS layer in my docker container I cannot shell into it - even if I add `-t -i` there is nothing.



