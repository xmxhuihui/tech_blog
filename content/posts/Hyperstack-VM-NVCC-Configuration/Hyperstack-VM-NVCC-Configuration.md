---
date: '2026-08-14T10:44:17+08:00'
draft: false
title: 'Hyperstack VM NVCC Configuration'
---

## Login through terminal
```bash
ssh -i <path_to_private_key> ubuntu@<public ip>
```

## NVCC installation
Go to https://developer.nvidia.com/cuda-toolkit-archive

Find the needed version of CUDA toolkit and choose installer type deb(network)

## Add environment variables
First, find the installed NVCC file path.
```bash
sudo find / -name nvcc 2>/dev/null
```
if it returns the path of installed nvcc, like
```bash
/usr/local/cuda-12.4/bin/nvcc
```
add it to the environment variables:
```bash
export PATH=/usr/local/cuda-12.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH
```

To add it every time of reboot, add the lines into .bashrc:
```bash
nano ~/.bashrc
```
```
export CUDA_HOME=/usr/local/cuda-12.4
export PATH=$CUDA_HOME/bin:$PATH
export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
```
Then check the NVCC with:
```bash
nvcc -V
```
You shall be able to see the version of activated NVCC.