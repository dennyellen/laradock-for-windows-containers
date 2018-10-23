# escape=`
FROM microsoft/windowsservercore AS installer
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ARG PHP_VERSION="7.2.11"
ARG PHP_TS="nts"
ARG PHP_PLATFORM="Win32-VC15-x64"
ARG PHP_DOWNLOAD_SHA256="74e3fbff785943c5b17c0859264f06f72f0a7bb1fbb2ace17040ec89ec560f03"

RUN Write-Host "Downloading PHP version: $env:PHP_VERSION"; `
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; `
    Invoke-WebRequest -OutFile php.zip -UseBasicParsing "https://windows.php.net/downloads/releases/php-$($env:PHP_VERSION)-$($env:PHP_TS)-$($env:PHP_PLATFORM).zip";

RUN if ((Get-FileHash php.zip -Algorithm sha256).Hash -ne $env:PHP_DOWNLOAD_SHA256) {exit 1}; `
	Expand-Archive -Path php.zip -DestinationPath C:\php; `
	Remove-Item php.zip -Force

# PHP
FROM microsoft/windowsservercore

ARG PHP_VERSION="7.2.11"
ENV PHP_VERSION ${PHP_VERSION}

# EXPOSE 80 443
WORKDIR C:\php
CMD "C:\php\php.exe"

COPY --from=installer C:\php\ .