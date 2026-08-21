---
date: '2026-08-21T19:14:29+08:00'
draft: false
title: 'Terminal Using Clash Proxy'
---
```bash
Set-ExecutionPolicy Bypass -Scope Process
```
Create a new file proxy1.ps1
```
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