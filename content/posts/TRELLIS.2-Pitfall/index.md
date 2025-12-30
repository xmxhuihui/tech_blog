---
date: '2025-12-30T16:00:28Z'
draft: false
title: 'TRELLIS.2 Pitfall'
---

```bash
Traceback (most recent call last):
  File "/workspace/TRELLIS.2/example.py", line 24, in <module>
    image = Image.open("assets/example_image/panda-cartoon-cute-little-mascot.webp")
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/opt/conda/lib/python3.11/site-packages/PIL/Image.py", line 3281, in open
    if init():
       ^^^^^^
  File "/opt/conda/lib/python3.11/site-packages/PIL/Image.py", line 381, in init
    __import__(f"PIL.{plugin}", globals(), locals(), [])
  File "/opt/conda/lib/python3.11/site-packages/PIL/WebPImagePlugin.py", line 363, in <module>
    if _webp.HAVE_WEBPANIM:
       ^^^^^^^^^^^^^^^^^^^
AttributeError: module 'PIL._webp' has no attribute 'HAVE_WEBPANIM'
```
Solution:
1. ```glb.export("sample.glb", extension_webp=False)```
2. ```apt-get update```
3. ```apt-get install -y libwebp-dev```
4. ```pip install -U pillow```