FROM jenkins/jnlp-slave:3.19-1

USER root

# nodejs
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - \
  && apt-get install -y nodejs

# docker cli
RUN apt-get update \
  && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common \
  && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
  && apt-key fingerprint 0EBFCD88 \
  && add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   xenial \
   stable" \
  && apt-get update \
  && apt-get install -y \
    docker-ce

# Ansible
RUN apt-get update \
  && apt-get install -y software-properties-common \
  && apt-add-repository "deb http://ppa.launchpad.net/ansible/ansible/ubuntu xenial main" \
  && apt-get update \
  && apt-get install -y --allow-unauthenticated ansible

# dotnet
ENV NUGET_XMLDOC_MODE=skip \
  DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1 \
  DOTNET_CLI_TELEMETRY_OPTOUT=1

RUN curl -sSL -o dotnet.tar.gz https://download.microsoft.com/download/D/0/4/D04C5489-278D-4C11-9BD3-6128472A7626/dotnet-sdk-2.1.301-linux-x64.tar.gz \
    && mkdir -p /opt/dotnet \
    && tar zxf dotnet.tar.gz -C /opt/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /opt/dotnet/dotnet /usr/local/bin

# mono
ENV MONO_VERSION 4.8.0.495

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF

RUN echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots/$MONO_VERSION main\n\
deb http://download.mono-project.com/repo/debian wheezy-apache24-compat main\n\
deb http://download.mono-project.com/repo/debian wheezy-libjpeg62-compat main" > /etc/apt/sources.list.d/mono-xamarin.list \
  && apt-get update \
  && apt-get install -y binutils mono-devel ca-certificates-mono nuget referenceassemblies-pcl \
  && rm -rf /var/lib/apt/lists/* /tmp/*

USER jenkins