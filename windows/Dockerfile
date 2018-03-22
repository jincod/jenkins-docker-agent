FROM openjdk:9.0.1-jdk-windowsservercore-ltsc2016

RUN NET USER jenkins /add
RUN NET LOCALGROUP Administrators /add jenkins

USER jenkins

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# .NET Framwork v4.6.2
RUN Invoke-WebRequest "https://download.microsoft.com/download/E/F/D/EFD52638-B804-4865-BB57-47F4B9C80269/NDP462-DevPack-KB3151934-ENU.exe" -OutFile NDP462-DevPack-KB3151934-ENU.exe -UseBasicParsing; \
    cmd /c start /WAIT NDP462-DevPack-KB3151934-ENU.exe /install /quiet; \
    rm NDP462-DevPack-KB3151934-ENU.exe

# .NET Framwork v4.7.1
RUN Invoke-WebRequest "https://download.microsoft.com/download/9/0/1/901B684B-659E-4CBD-BEC8-B3F06967C2E7/NDP471-DevPack-ENU.exe" -OutFile NDP471-DevPack-ENU.exe -UseBasicParsing; \
    cmd /c start /WAIT NDP471-DevPack-ENU.exe /install /quiet /norestart; \
    rm NDP471-DevPack-ENU.exe

# MSBuid 14.0
RUN Invoke-WebRequest "https://download.microsoft.com/download/E/E/D/EEDF18A8-4AED-4CE0-BEBE-70A83094FC5A/BuildTools_Full.exe" -OutFile BuildTools_Full.exe -UseBasicParsing; \
    cmd /c start /WAIT BuildTools_Full.exe /Silent /Full; \
    rm BuildTools_Full.exe

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

# Ssl3, Tls, Tls11, Tls12
RUN [System.Net.ServicePointManager]::SecurityProtocol = 3072 -bor 768 -bor 192 -bor 48; \
    Invoke-WebRequest $('https://github.com/git-for-windows/git/releases/download/v{0}.windows.1/Git-{0}-64-bit.exe' -f $env:GIT_VERSION) -OutFile git-installer.exe -UseBasicParsing; \
    cmd /c start /WAIT git-installer.exe /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /NOCANCEL /SP- /LOG /o:UseCredentialManager=Disabled; \
    rm git-installer.exe

# node.js
ARG NPM_CONFIG_LOGLEVEL=info
ARG NODE_VERSION=8.9.3
ARG NODE_SHA256=68cdd5a021101a208d830da016eebb3a9de5ba75e76d8d8d6e9332038f10f56a

RUN Invoke-WebRequest $('https://nodejs.org/dist/v{0}/node-v{0}-x64.msi' -f $env:NODE_VERSION) -OutFile node.msi -UseBasicParsing ; \
    if ((Get-FileHash node.msi -Algorithm sha256).Hash -ne $env:NODE_SHA256) {exit 1} ; \
    Start-Process -FilePath msiexec -ArgumentList /q, /i, node.msi -Wait ; \
    rm node.msi

ARG VERSION=3.9

ENV JENKINS_URL= \
    JENKINS_AGENT_NAME= \
    JENKINS_SECRET= \
    USERNAME= \
    USER_TOKEN=

RUN Invoke-WebRequest -Uri $('https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/{0}/remoting-{0}.jar' -f $env:VERSION) -UseBasicParsing -OutFile slave.jar; \
    mkdir \jenkins

# VOLUME C:\\jenkins

CMD java -jar slave.jar -noCertificateCheck -jnlpUrl $env:JENKINS_URL/computer/$env:JENKINS_AGENT_NAME/slave-agent.jnlp -secret $env:JENKINS_SECRET