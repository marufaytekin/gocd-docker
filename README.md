# gocd-docker

This repo contains docker files and required configuration files to build and run gocd server and agent.  



```
docker-compose up
```
wait for it to be fully started, it can take a while. You're looking for something like this in the logs
```
gocd-server    | == Thu Feb 16 20:39:12 UTC 2017: Waiting for Go Server dashboard to be accessible ...
gocd-server    | (unknown):-1 warning: already initialized constant Input
gocd-server    |
gocd-server    |
gocd-server    | ----------------------------------------------------------
gocd-server    | Go Server has started on port 8153 inside this container (453390d83f71)!
gocd-server    |
gocd-server    | To be able to connect to it in a browser, you need to find the port which has been mapped to port 8153 for this container.
gocd-server    |
gocd-server    | If you're using docker on a Linux box, you can do this:
gocd-server    | echo http://localhost:$(docker inspect --format='{{(index (index .NetworkSettings.Ports "8153/tcp") 0).HostPort}}' 453390d83f71)
gocd-server    |
gocd-server    | If you're using docker through boot2docker, on a Mac, do this:
gocd-server    | echo http://$(docker-machine ip):$(docker inspect --format='{{(index (index .NetworkSettings.Ports "8153/tcp") 0).HostPort}}' 453390d83f71)
gocd-server    |
gocd-server    | That command will output the URL through which you should be able to access the Go Server.
gocd-server    | ----------------------------------------------------------
gocd-server    |
gocd-server    |
gocd-server    | Thu Feb 16 20:39:18 UTC 2017: This message will stay for 30 seconds and then the Go Server logs will be shown. Run docker with the option: '-e MSG_TIME=0' to disable this wait time.
```

go to http://localhost:8080/go


# Running without docker-compose

gocd-agent includes following:

+ docker client to build and deploy containers.
+ nodejs agent


## build images:

+ data for data persistency
+ server
+ agent

```bash
docker build -t gocd-data gocd-data/.
docker build -t gocd-server gocd-server/.
docker build -t gocd-agent gocd-agent/.
```

## create / run containers
create data container:

```bash
docker create --name gocd-data gocd-data
```

Note: No need to run this container.

run gocd-server container and load volumes from gocd-data container:

```bash
docker run -d -t -p 8153:8153 -p 8154:8154 --name gocd-server --volumes-from gocd-data gocd-server
```

run as many gocd build agent containers as you like:
```bash
docker run -e GO_SERVER_URL="https://gocd-server:8154/go" -v /var/run/docker.sock:/var/run/docker.sock -d --name <AGENT_NAME> --link gocd-server:go-server gocd-agent
```

You can run 4 build agents as follows:

```bash
docker run -e GO_SERVER_URL="https://gocd-server:8154/go" -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent1 --link gocd-server:go-server gocd-agent
docker run -e GO_SERVER_URL="https://gocd-server:8154/go" -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent2 --link gocd-server:go-server gocd-agent
docker run -e GO_SERVER_URL="https://gocd-server:8154/go" -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent3 --link gocd-server:go-server gocd-agent
docker run -e GO_SERVER_URL="https://gocd-server:8154/go" -v /var/run/docker.sock:/var/run/docker.sock -d --name gocd-agent4 --link gocd-server:go-server gocd-agent
```

We expose the docker socket to goCD agent containers, by bind-mounting it with the ```-v``` flag.
You can check [this post](http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) for more information.

> Note: Make sure gocd-agent users have read/write permissions to ```/var/run/docker.sock``` file.  
