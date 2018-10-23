# escape=`
FROM microsoft/windowsservercore AS installer
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# PHP
ARG PHP_VERSION="7.2.11"
ARG PHP_TS="nts"
ARG PHP_PLATFORM="Win32-VC15-x64"
ARG PHP_DOWNLOAD_SHA256="74e3fbff785943c5b17c0859264f06f72f0a7bb1fbb2ace17040ec89ec560f03"

# git
ARG GIT_VERSION="2.17.1"
ARG GIT_RELEASE_NUMBER="2"
ARG GIT_DOWNLOAD_SHA256="52e611a411cd58eaaab8218bb917cb4410b0c5733f234be6e581c6a9821b30ea"

RUN Write-Host "Downloading PHP version: $env:PHP_VERSION"; `
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; `
    Invoke-WebRequest -OutFile php.zip -UseBasicParsing "https://windows.php.net/downloads/releases/php-$($env:PHP_VERSION)-$($env:PHP_TS)-$($env:PHP_PLATFORM).zip";

RUN if ((Get-FileHash php.zip -Algorithm sha256).Hash -ne $env:PHP_DOWNLOAD_SHA256) {exit 1}; `
	Expand-Archive -Path php.zip -DestinationPath C:\php; `
	Remove-Item php.zip -Force

# git
RUN Write-Host "Downloading Git version: $($env:GIT_VERSION), release: $($env:GIT_RELEASE_NUMBER)"; `
	[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; `
    Invoke-WebRequest -OutFile git.zip -Uri "https://github.com/git-for-windows/git/releases/download/v$($env:GIT_VERSION).windows.$($env:GIT_RELEASE_NUMBER)/MinGit-$($env:GIT_VERSION).$($env:GIT_RELEASE_NUMBER)-64-bit.zip"

RUN if ((Get-FileHash git.zip -Algorithm sha256).Hash -ne $env:GIT_DOWNLOAD_SHA256) {exit 1}; `
	Expand-Archive -Path git.zip -DestinationPath C:\git; `
	Remove-Item git.zip -Force


FROM microsoft/windowsservercore
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';"]

# PHP
ARG PHP_VERSION="7.2.11"
# Git
ARG GIT_VERSION="2.17.1"

ENV PHP_VERSION ${PHP_VERSION} `
    GIT_VERSION=${GIT_VERSION} `
    GIT_PATH="C:\git\cmd;C:\git\mingw64\bin;C:\git\usr\bin;" 

RUN $env:PATH = $env:GIT_PATH + $env:PATH; `
	[Environment]::SetEnvironmentVariable('PATH', $env:PATH, [EnvironmentVariableTarget]::Machine)

WORKDIR C:\php
CMD "C:\php\php.exe"

COPY --from=installer C:\php\ .

WORKDIR C:\git
COPY --from=installer C:\git .