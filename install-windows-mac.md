# Installation instructions for windows and mac

On further investigation this appears to be deprecated by docker-machine - will remove from my main doc.

https://docs.docker.com/machine/migrate-to-machine/


### On Windows

Just notes - I'll never ever do this! - has an extra layer on top of windows, boot2Docker - there is a CoreOS VM, docker then runs in there.

NOTE: uninstall virtual box first, as coreOS runs on VirtualBox.

### On Mac

As linux? - not sure if it runs native or on top of vagrant.
```
Steps to install on mac
1. download boot2docker for mac

2. run boot2docker up

3. it will print out some export commands. make sure you enter them into the .bash_profile

(for me they were)

export DOCKER_HOST=tcp://192.168.59.103:2376

export DOCKER_CERT_PATH=/Users/abhi/.boot2docker/certs/boot2docker-vm

export DOCKER_TLS_VERIFY=1


4. now run hello world

docker run busybox echo Hello
```

