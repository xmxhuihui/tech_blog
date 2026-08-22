---
date: '2026-08-21T19:14:29+08:00'
draft: false
title: 'Terminal Using Clash Proxy'
---
## Powershell
```bash
Set-ExecutionPolicy Bypass -Scope Process
```
Create a new file proxy1.ps1
```ps1
$port = xxxx

$env:HTTP_PROXY="http://127.0.0.1:$port"
$env:HTTPS_PROXY="http://127.0.0.1:$port"
$env:http_proxy="http://127.0.0.1:$port"
$env:https_proxy="http://127.0.0.1:$port"
```
$port is that of clash proxy.

Then run proxy1.ps1 in powershell.
```bash
. proxy1.ps1
```
Replace it with the file path of proxy1.ps1

### VSCode
Go to the target folder and open powershell, run ```code .```
## CMD
Create a file called proxy1.bat with the content:
```bat
@echo off
set "HTTP_PROXY=http://127.0.0.1:7897"
set "HTTPS_PROXY=http://127.0.0.1:7897"
set "http_proxy=http://127.0.0.1:7897"
set "https_proxy=http://127.0.0.1:7897"

echo Proxy enabled: 127.0.0.1:7897
```
And run the command in the CMD:
```bash
call .proxy1.bat
```
replace it with the file path of proxy1.bat