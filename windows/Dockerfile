FROM openjdk:9.0.4-jdk-windowsservercore-ltsc2016

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

RUN NET USER jenkins /add
RUN NET LOCALGROUP Administrators /add jenkins
RUN Set-LocalUser -Name jenkins -PasswordNeverExpires 1

USER jenkins

# Install .NET 4.7.2
RUN Invoke-WebRequest "https://download.microsoft.com/download/5/A/3/5A3607CA-53B1-4717-8845-4389B11931FA/NDP472-KB4054530-x86-x64-AllOS-ENU.exe" -OutFile dotnet-framework-installer.exe; \
    Start-Process -Wait -PassThru -FilePath dotnet-framework-installer.exe -ArgumentList '/q'; \
    rm dotnet-framework-installer.exe

# Build Tools
# https://www.visualstudio.com/downloads/#other
# https://docs.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools
RUN Invoke-WebRequest "https://download.visualstudio.microsoft.com/download/pr/12221253/e64d79b40219aea618ce2fe10ebd5f0d/vs_BuildTools.exe" -OutFile vs_BuildTools.exe -UseBasicParsing; \
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
ENV GIT_VERSION=2.11.1

# Set certificate policy to allow connections for SSL3, TLS, TLS12 and TLS12
RUN [System.Net.ServicePointManager]::SecurityProtocol = 3072 -bor 768 -bor 192 -bor 48; \
    Invoke-WebRequest $('https://github.com/git-for-windows/git/releases/download/v{0}.windows.1/Git-{0}-64-bit.exe' -f $env:GIT_VERSION) -OutFile git-installer.exe -UseBasicParsing; \
    cmd /c start /WAIT git-installer.exe /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /NOCANCEL /SP- /LOG /o:UseCredentialManager=Disabled; \
    rm git-installer.exe

ARG GIT_MAIL_ADDRESS=jenkins.slave@example.com
ARG GIT_USERNAME=Jenkins

RUN git config --global user.email "$env:GIT_MAIL_ADDRESS"; \
    git config --global user.name "$env:GIT_USERNAME"

# node.js
ARG NPM_CONFIG_LOGLEVEL=info
ARG NODE_VERSION=8.9.3
ARG NODE_SHA256=68cdd5a021101a208d830da016eebb3a9de5ba75e76d8d8d6e9332038f10f56a

RUN Invoke-WebRequest $('https://nodejs.org/dist/v{0}/node-v{0}-x64.msi' -f $env:NODE_VERSION) -OutFile node.msi -UseBasicParsing ; \
    if ((Get-FileHash node.msi -Algorithm sha256).Hash -ne $env:NODE_SHA256) {exit 1} ; \
    Start-Process -FilePath msiexec -ArgumentList /q, /i, node.msi -Wait ; \
    rm node.msi

ARG VERSION=3.20

ENV JENKINS_URL= \
    JENKINS_AGENT_NAME= \
    JENKINS_SECRET= \
    USERNAME= \
    USER_TOKEN=

RUN Invoke-WebRequest -Uri $('https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/{0}/remoting-{0}.jar' -f $env:VERSION) -UseBasicParsing -OutFile slave.jar; \
    mkdir \jenkins

# VOLUME C:\\jenkins

CMD java -jar slave.jar -noCertificateCheck -jnlpUrl $env:JENKINS_URL/computer/$env:JENKINS_AGENT_NAME/slave-agent.jnlp -secret $env:JENKINS_SECRET