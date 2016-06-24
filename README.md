# gocd-docker 

This repo contains docker files and required configuration files to build and run gocd server and agent.  

gocd-agent includes following:

+ docker client to build and deploy containers.
+ maven, ant, and nodejs.
+ flyway
+ capybara, cucumber, selenium, firefox, and Xvfb.


## gocd-server 

build a data container for data persistency:

```bash
cd gocd-data
docker build -t gocd-data .
```
create data container:

```bash
docker create --name gocd-data gocd-data
```

Note: No need to run this container.

build and run gocd-server container and load volumes from gocd-data container:

```bash
cd gocd-server
docker build -t gocd-server .
docker run -d -t -p 8153:8153 -p 8154:8154 --name gocd-server --volumes-from gocd-data gocd-server
```

## gocd-agent

build gocd-agent:

```bash
cd gocd-agent
docker build -t gocd-agent .
```
run as many gocd build agent containers as you like:
```bash
docker run -v /var/run/docker.sock:/var/run/docker.sock -d --name <AGENT_NAME> --link gocd-server:go-server gocd-agent
```

You can run 4 build agents as follows:

```bash
docker run -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent1 --link gocd-server:go-server gocd-agent
docker run -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent2 --link gocd-server:go-server gocd-agent
docker run -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent3 --link gocd-server:go-server gocd-agent
docker run -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent4 --link gocd-server:go-server gocd-agent
```

We expose the docker socket to goCD agent containers, by bind-mounting it with the ```-v``` flag.
You can check [this post](http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) for more information.

Note: Make sure gocd-agent users have read/write permissions to ```/var/run/docker.sock``` file.  



