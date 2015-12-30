## Running Git in a Docker Container

http://stackoverflow.com/questions/23391839/clone-private-git-repo-with-dockerfile

Useful stuff from the above discussion:

>
	You should create new SSH key set for that Docker image, as you probably don't want to embed there your own private key. To make it work, you'll have to add that key to deployment keys in your git repository. Here's complete recipe:

	Generate ssh keys with ssh-keygen -q -t rsa -N '' -f repo-key which will give you repo-key and repo-key.pub files.

	Add repo-key.pub to your repository deployment keys.
	On GitHub, go to [your repository] -> Settings -> Deploy keys

	Add something like this to your Dockerfile:

	ADD repo-key /
	RUN \
	  chmod 600 /repo-key && \  
	  echo "IdentityFile /repo-key" >> /etc/ssh/ssh_config && \  
	  echo -e "StrictHostKeyChecking no" >> /etc/ssh/ssh_config && \  
	  // your git clone commands here...
	Note that above switches off StrictHostKeyChecking, so you don't need .ssh/known_hosts. Although I probably like more the solution with ssh-keyscan in one of the answers above.
>


Ok - also node_env looks like it will be a pain.

```
#!/bin/bash
gulp build
export NODE_ENV=production
nodemon server -p80
```

seen that as a work around - with npm pulls etc.


Note there is some -dns x.x.x.x - flag that can be used on docker run.

Build command for go to be a STATIC binary file (no lib deps!) can run on an empty docker build
``
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -a -installsuffix cgo -o main . 
``


