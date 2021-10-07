FROM jenkins/inbound-agent:4.10-3

USER root

# nodejs
RUN apt-get update \
    && apt install -y curl \
    && curl -sL https://deb.nodesource.com/setup_14.x | bash - \
    && apt-get install -y nodejs

# docker cli
RUN apt-get update \
  && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common \
  && curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - \
  && apt-key fingerprint 0EBFCD88 \
  && add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable" \
  && apt-get update \
  && apt-get install -y --fix-broken \
    docker-ce docker-ce-cli containerd.io

# Ansible
RUN apt-get update \
  && apt-get install -y software-properties-common \
  && apt-add-repository "deb http://ppa.launchpad.net/ansible/ansible/ubuntu xenial main" \
  && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367 \
  && apt-get update \
  && apt-get install -y --allow-unauthenticated ansible

# AWS CLI
RUN apt-get update && \
    apt-get install -y \
        python3 \
        python3-pip \
        python3-setuptools \
        groff \
        less \
    && python3 -m pip install --upgrade pip \
    && apt-get clean \
    && python3 -m pip --no-cache-dir install --upgrade awscli

# azure-cli
RUN apt-get update && \
    apt-get -f -y install curl apt-transport-https lsb-release gnupg python3-pip && \
    curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > /etc/apt/trusted.gpg.d/microsoft.asc.gpg && \
    CLI_REPO=$(lsb_release -cs) && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ ${CLI_REPO} main" \
    > /etc/apt/sources.list.d/azure-cli.list \
    && apt-get update \
    && apt-get install -y azure-cli \
    && rm -rf /var/lib/apt/lists/*

# dotnet
ENV NUGET_XMLDOC_MODE=skip \
  DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1 \
  DOTNET_CLI_TELEMETRY_OPTOUT=1

RUN curl -sSL -o dotnet.tar.gz https://download.visualstudio.microsoft.com/download/pr/5972698f-ba44-4664-9c50-bdc69ca70fb7/1cf7d94425d8dd4d5789dfa978d61475/dotnet-sdk-5.0.401-linux-x64.tar.gz \
    && mkdir -p /opt/dotnet \
    && tar zxf dotnet.tar.gz -C /opt/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /opt/dotnet/dotnet /usr/local/bin

# mono
# ENV MONO_VERSION 5.10.1.20

# RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF \
#   && echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots/$MONO_VERSION main\n\
# deb http://download.mono-project.com/repo/debian wheezy-apache24-compat main\n\
# deb http://download.mono-project.com/repo/debian wheezy-libjpeg62-compat main" > /etc/apt/sources.list.d/mono-xamarin.list \
#   && apt-get update \
#   && apt-get install -y binutils mono-devel ca-certificates-mono nuget referenceassemblies-pcl \
#   && rm -rf /var/lib/apt/lists/* /tmp/*

USER ${user}