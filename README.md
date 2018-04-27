# Jenkins slave Docker images

[![Build status](https://ci.appveyor.com/api/projects/status/t54hav6qci21bkr2?svg=true)](https://ci.appveyor.com/project/jincod/docker-jenkins-slave)


## Linux

Based on [jnlp-slave](https://github.com/jenkinsci/docker-jnlp-slave)

### Usage

[View](https://github.com/jenkinsci/docker-jnlp-slave#running) available options.

```bash
docker build -t docker-jenkins-slave-linux .
docker run -v /var/run/docker.sock:/var/run/docker.sock --env JENKINS_URL=https://jenkins --env JENKINS_SECRET=xxx --env JENKINS_AGENT_NAME=agent-name docker-jenkins-slave-linux
```

## Windows

Based on [9.0.1-jdk-windowsservercore-1709](https://github.com/docker-library/openjdk/blob/master/9-jdk/windows/windowsservercore-1709/Dockerfile)

### Usage

```bash
docker build -t docker-jenkins-slave-windows  .
docker run -m 2GB --env JENKINS_URL=https://jenkins --env JENKINS_SECRET=xxx --env JENKINS_AGENT_NAME=agent-name docker-jenkins-slave-windows
```