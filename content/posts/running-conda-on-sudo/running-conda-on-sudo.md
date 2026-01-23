---
date: '2026-01-23T00:24:38Z'
draft: true
title: 'Running Conda on Sudo'
---
```bash
sudo env "PATH=$PATH" conda xxx
```

if running ```pip install requirements.txt```, and conda command is in ```requirements.txt```, add ```sudo env "PATH=$PATH" ``` before pip install.