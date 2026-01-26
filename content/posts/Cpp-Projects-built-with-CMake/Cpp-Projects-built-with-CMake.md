---
date: '2026-01-26T22:19:09Z'
draft: true
title: 'Cpp Projects Built With CMake'
---
```BASH
cmake -S . -B build-vs2026 -G "Visual Studio 18 2026" -A x64 # configure
cmake --build build-vs2026 --config Release # build
```
If needed, add the buildup folders/subfolders to system path, or copy the files into the project folder.