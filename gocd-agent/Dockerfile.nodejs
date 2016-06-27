###########################################################
# Build using: docker build -f Dockerfile.nodejs -t gocd-agent .
# based on official gocd/gocd-agent latest image.
# installs:
#    docker engine to enable docker in docker
#    nodejs for builds
###########################################################
FROM gocd/gocd-agent:latest

MAINTAINER marufaytekin

###########################################################
# install docker engine to use docker client # from the agent.
# In order to use docker client make sure you give r/w
# prmissions to /var/run/docker.sock file on the docker host.
###########################################################
RUN touch /etc/apt/sources.list.d/docker.list \
  && echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" >>/etc/apt/sources.list.d/docker.list \
  && apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D \
  && apt-get update \
  && apt-get install -y --force-yes docker-engine \
  && usermod -aG docker go


###########################################################
# install nodejs
###########################################################
RUN echo "go    ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers \
 && su - go -c "touch ~/.bashrc" \
 && su - go -c "curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh | bash" \
 && su - go -c ". ~/.nvm/nvm.sh && nvm install 5.9.0" \
 && su - go -c "touch ~/.npmrc" \
 && su - go -c "echo strict-ssl=false >> ~/.npmrc" \
 && su - go -c "echo config="strict-ssl=false" >> ~/.npmrc" \
 && su - go -c "echo minTimeout=300000 >> ~/.npmrc" \
