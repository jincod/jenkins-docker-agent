FROM jenkins/inbound-agent:3355.v388858a_47b_33-19-jdk25

USER root

# nodejs
RUN apt-get update \
    && apt install -y curl \
    && curl -sL https://deb.nodesource.com/setup_24.x | bash - \
    && apt-get install -y nodejs

# docker cli
RUN apt-get update \
  && apt-get install -y \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg \
      lsb-release \
  && install -m 0755 -d /etc/apt/keyrings \
  && curl -fsSL https://download.docker.com/linux/debian/gpg \
      | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
  && chmod a+r /etc/apt/keyrings/docker.gpg \
  && echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/debian \
      $(lsb_release -cs) stable" \
      > /etc/apt/sources.list.d/docker.list \
  && apt-get update \
  && apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Ansible
RUN apt-get update \
  && apt-get install -y ansible

# temporary disabled
# # AWS CLI
# RUN apt-get update && \
#     apt-get install -y \
#         python3 \
#         python3-pip \
#         python3-setuptools \
#         groff \
#         less \
#     && python3 -m pip install --upgrade pip \
#     && apt-get clean \
#     && python3 -m pip --no-cache-dir install --upgrade awscli

# # azure-cli
# RUN apt-get update && \
#     apt-get -f -y install curl apt-transport-https lsb-release gnupg python3-pip && \
#     curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > /etc/apt/trusted.gpg.d/microsoft.asc.gpg && \
#     CLI_REPO=$(lsb_release -cs) && \
#     echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ ${CLI_REPO} main" \
#     > /etc/apt/sources.list.d/azure-cli.list \
#     && apt-get update \
#     && apt-get install -y azure-cli \
#     && rm -rf /var/lib/apt/lists/*

# dotnet
ENV NUGET_XMLDOC_MODE=skip \
  DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1 \
  DOTNET_CLI_TELEMETRY_OPTOUT=1

RUN curl -sSL -o dotnet.tar.gz https://builds.dotnet.microsoft.com/dotnet/Sdk/10.0.202/dotnet-sdk-10.0.202-linux-x64.tar.gz \
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