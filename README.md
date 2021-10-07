# Jenkins Agent Docker images

[![Build status](https://ci.appveyor.com/api/projects/status/t54hav6qci21bkr2?svg=true)](https://ci.appveyor.com/project/jincod/jenkins-docker-agent)


## Linux

Based on [jenkinsci/docker-inbound-agent](https://github.com/jenkinsci/docker-inbound-agent)

### Usage

[View](https://github.com/jenkinsci/docker-inbound-agent#running) available options.

```bash
docker build -t jenkins-agent-linux .
docker run -v /var/run/docker.sock:/var/run/docker.sock --env JENKINS_URL=https://jenkins --env JENKINS_SECRET=xxx --env JENKINS_AGENT_NAME=agent-name jenkins-agent-linux
```

## Windows

Based on [jdk11-windowsservercore-ltsc2019](https://github.com/jenkinsci/docker-agent/blob/master/11/windows/windowsservercore-ltsc2019/Dockerfile)

### Usage

```bash
docker build -t jenkins-agent-windows  .
docker run -m 2GB --env JENKINS_URL=https://jenkins --env JENKINS_SECRET=xxx --env JENKINS_AGENT_NAME=agent-name jenkins-agent-windows
```