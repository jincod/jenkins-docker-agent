FROM jenkins/agent:4.10-3-jdk11-windowsservercore-ltsc2019

# Install .NET 4.7.2
RUN Invoke-WebRequest -Uri "https://download.visualstudio.microsoft.com/download/pr/158dce74-251c-4af3-b8cc-4608621341c8/9c1e178a11f55478e2112714a3897c1a/ndp472-devpack-enu.exe" -OutFile dotnet-framework-installer.exe; \
    Start-Process -Wait -PassThru -FilePath dotnet-framework-installer.exe -ArgumentList '/q'; \
    Remove-Item dotnet-framework-installer.exe

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
ARG GIT_MAIL_ADDRESS=jenkins.agent@example.com
ARG GIT_USERNAME=Jenkins

RUN git config --global user.email "$env:GIT_MAIL_ADDRESS"; \
    git config --global user.name "$env:GIT_USERNAME"

# node.js
ARG NPM_CONFIG_LOGLEVEL=info
ARG NODE_VERSION=12.14.1
ARG NODE_SHA256=630bc34155e7fdb65c02ed44a37cd27dcf3f76a061c220e7af8baacdb0c2bb9c

RUN Invoke-WebRequest $('https://nodejs.org/dist/v{0}/node-v{0}-x64.msi' -f $env:NODE_VERSION) -OutFile node.msi -UseBasicParsing ; \
    if ((Get-FileHash node.msi -Algorithm sha256).Hash -ne $env:NODE_SHA256) {exit 1} ; \
    Start-Process -FilePath msiexec -ArgumentList /q, /i, node.msi -Wait ; \
    rm node.msi

ENV JENKINS_URL= \
    JENKINS_AGENT_NAME= \
    JENKINS_SECRET= \
    USERNAME= \
    USER_TOKEN=

# VOLUME C:/Users/jenkins/Work

CMD java -jar C:/ProgramData/Jenkins/agent.jar -workDir C:/Users/jenkins/Work -jnlpUrl $env:JENKINS_URL/computer/$env:JENKINS_AGENT_NAME/jenkins-agent.jnlp -secret $env:JENKINS_SECRET
