###########################################################
# Build using: docker build -t gocd-agent .
# based on official gocd/gocd-agent latest image.
# installs:
#    docker engine to enable docker in docker
#    maven, ant, and nodejs for builds
#    flyway to automate database updates
#    ruby, capybara, and cucumber requirements to run automated tests.
###########################################################
 
FROM gocd/gocd-agent:latest

###########################################################
# add docker repos, install docker engine to use docker client
# from the agent. In order to use docker client make sure you give r/w
# prmissions to /var/run/docker.sock file on the docker host
# which runs gocd agent container.
###########################################################
RUN touch /etc/apt/sources.list.d/docker.list \
  && echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" >>/etc/apt/sources.list.d/docker.list \
  && apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D \
  && apt-get update \
  && apt-get install -y --force-yes docker-engine \
  && usermod -aG docker go


###########################################################
# installs maven and ant.
###########################################################
RUN apt-get install -y --force-yes maven


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


###########################################################
# install flyway commandline to run database updates.
###########################################################
RUN curl -O --insecure http://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/4.0/flyway-commandline-4.0-linux-x64.tar.gz \
    && cd /opt; tar -xzvf /tmp/flyway-commandline-4.0-linux-x64.tar.gz \
    && chown -R  go:go flyway-4.0; ln -s flyway-4.0/ flyway \
    && chown -R go:go flyway \
    && rm /tmp/flyway-commandline-4.0-linux-x64.tar.gz


###########################################################
# install ruby, capybara, and cucumber test requirements.
###########################################################
ADD Gemfile /var/go
RUN apt-add-repository ppa:brightbox/ruby-ng \
 && apt-get update \
 && apt-get install -y --force-yes \
      software-properties-common \
      build-essential \
      zlib1g-dev \
      ruby1.9.3 \
      firefox \
      xvfb \
 && rm -rf /var/lib/apt/lists/* \
 && gem install bundler \
 && su - go -c "bundle install"
