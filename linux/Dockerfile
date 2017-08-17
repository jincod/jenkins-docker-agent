FROM jenkinsci/jnlp-slave

USER root

# nodejs
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
  && apt-get install -y nodejs

# docker cli
RUN apt-get update \
  && apt-get install -y \
    docker.io

# dotnet
RUN apt-get update \
  && apt-get install -y -q \
    curl \
    libunwind8 \
    gettext \
  && rm -rf /var/lib/apt/lists/*

RUN curl -sSL -o dotnet.tar.gz https://go.microsoft.com/fwlink/?linkid=843453
RUN mkdir -p /opt/dotnet && tar zxf dotnet.tar.gz -C /opt/dotnet
RUN ln -s /opt/dotnet/dotnet /usr/local/bin

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