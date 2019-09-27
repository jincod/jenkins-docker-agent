FROM openjdk:9.0.4-jdk-windowsservercore-ltsc2016

SHELL ["cmd", "/S", "/C"]

RUN NET USER jenkins /add & \
    NET LOCALGROUP Administrators /add jenkins & \
    WMIC USERACCOUNT WHERE Name='jenkins' SET PasswordExpires=FALSE

USER jenkins

# Install .NET 4.7.2
RUN powershell Invoke-WebRequest -Uri "https://download.microsoft.com/download/3/B/F/3BFB9C35-405D-45DF-BDAF-0EB57D047888/NDP472-DevPack-ENU.exe" -OutFile dotnet-framework-installer.exe & \
    .\dotnet-framework-installer.exe /q & \
    del .\dotnet-framework-installer.exe

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Build Tools
# https://www.visualstudio.com/downloads/#vstool-2017
# https://docs.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools
RUN Invoke-WebRequest "https://download.visualstudio.microsoft.com/download/pr/cbd6f837-08c6-4965-aa4d-99f6699a9689/472e894c5015dcc90e5c367f99d98bb9/vs_buildtools.exe" -OutFile vs_BuildTools.exe -UseBasicParsing; \
    setx /M DOTNET_SKIP_FIRST_TIME_EXPERIENCE 1; \
    Start-Process -Wait -PassThru -FilePath vs_BuildTools.exe -ArgumentList '--add Microsoft.VisualStudio.Workload.NetCoreBuildTools \
        --add Microsoft.VisualStudio.Workload.WebBuildTools \
        --add Microsoft.VisualStudio.Workload.MSBuildTools \
        --add Microsoft.Net.Component.4.5.1.TargetingPack \
        --add Microsoft.Net.Component.4.5.2.TargetingPack \
        --add Microsoft.Net.Component.4.5.TargetingPack \
        --add Microsoft.Net.Component.4.6.TargetingPack \
        --add Microsoft.Net.Component.4.TargetingPack \
        --add Microsoft.Net.ComponentGroup.TargetingPacks.Common \
        --add Microsoft.Net.Component.3.5.DeveloperTools \
        --add Microsoft.Net.Component.4.6.2.SDK \
        --add Microsoft.Net.Component.4.6.2.TargetingPack \
        --add Microsoft.Net.Component.4.7.1.SDK \
        --add Microsoft.Net.Component.4.7.1.TargetingPack \
        --add Microsoft.Net.Component.4.7.SDK \
        --add Microsoft.Net.Component.4.7.TargetingPack \
        --add Microsoft.Net.ComponentGroup.4.6.2.DeveloperTools \
        --add Microsoft.Net.ComponentGroup.4.7.1.DeveloperTools \
        --add Microsoft.Net.ComponentGroup.4.7.DeveloperTools \
        --quiet \
        --nocache \
        --norestart \
        --wait'; \
    rm vs_BuildTools.exe

# Web.targets
RUN Invoke-WebRequest "https://dist.nuget.org/win-x86-commandline/latest/nuget.exe" -OutFile nuget.exe -UseBasicParsing; \
    ./nuget.exe install MSBuild.Microsoft.VisualStudio.Web.targets -Version 14.0.0.3; \
    mkdir 'C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v14.0\WebApplications'; \
    cp 'MSBuild.Microsoft.VisualStudio.Web.targets.14.0.0.3\tools\VSToolsPath\*' 'C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v14.0' -Recurse -Force; \
    rm 'MSBuild.Microsoft.VisualStudio.Web.targets.14.0.0.3' -Recurse; \
    rm nuget.exe

# GIT
# https://support.cloudbees.com/hc/en-us/articles/221046888-Build-Hang-or-Fail-with-Git-for-Windows
ARG GIT_VERSION=2.11.1
ARG GIT_MAIL_ADDRESS=jenkins.slave@example.com
ARG GIT_USERNAME=Jenkins

# Set certificate policy to allow connections for SSL3, TLS, TLS12 and TLS12
RUN [System.Net.ServicePointManager]::SecurityProtocol = 3072 -bor 768 -bor 192 -bor 48; \
    Invoke-WebRequest $('https://github.com/git-for-windows/git/releases/download/v{0}.windows.1/Git-{0}-64-bit.exe' -f $env:GIT_VERSION) -OutFile git-installer.exe -UseBasicParsing; \
    cmd /c start /WAIT git-installer.exe /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /NOCANCEL /SP- /LOG /o:UseCredentialManager=Disabled; \
    rm git-installer.exe

RUN git config --global user.email "$env:GIT_MAIL_ADDRESS"; \
    git config --global user.name "$env:GIT_USERNAME"

# node.js
ARG NPM_CONFIG_LOGLEVEL=info
ARG NODE_VERSION=10.14.2
ARG NODE_SHA256=1e19793063ea30bc0c24fe19502e4b99453044bdf1ce57b6fb351dbbd5da7933

RUN Invoke-WebRequest $('https://nodejs.org/dist/v{0}/node-v{0}-x64.msi' -f $env:NODE_VERSION) -OutFile node.msi -UseBasicParsing ; \
    if ((Get-FileHash node.msi -Algorithm sha256).Hash -ne $env:NODE_SHA256) {exit 1} ; \
    Start-Process -FilePath msiexec -ArgumentList /q, /i, node.msi -Wait ; \
    rm node.msi

ARG VERSION=3.35

ENV JENKINS_URL= \
    JENKINS_AGENT_NAME= \
    JENKINS_SECRET= \
    USERNAME= \
    USER_TOKEN=

RUN [System.Net.ServicePointManager]::SecurityProtocol = 3072 -bor 768 -bor 192 -bor 48; \
    Invoke-WebRequest -Uri $('https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/{0}/remoting-{0}.jar' -f $env:VERSION) -UseBasicParsing -OutFile slave.jar; \
    mkdir \jenkins

# VOLUME C:\\jenkins

CMD java -jar slave.jar -jnlpUrl $env:JENKINS_URL/computer/$env:JENKINS_AGENT_NAME/slave-agent.jnlp -secret $env:JENKINS_SECRET
