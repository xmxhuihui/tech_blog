---
date: '2025-06-06T17:15:32+01:00'
draft: false
title: 'Loading Lora Weights in Stable Diffusion Model'
---
LoRAs also need to be used with another model:
```python
from diffusers import AutoPipelineForText2Image
import torch

pipeline = AutoPipelineForText2Image.from_pretrained("stabilityai/stable-diffusion-xl-base-1.0", torch_dtype=torch.float16).to("cuda")
```
Then use the load_lora_weights() method to load the ostris/super-cereal-sdxl-lora weights and specify the weights filename from the repository:
```python
pipeline.load_lora_weights("ostris/super-cereal-sdxl-lora", weight_name="cereal_box_sdxl_v1.safetensors")
prompt = "bears, pizza bites"
image = pipeline(prompt).images[0]
image
```

Replace the <span style="color: red;">Model</span> and the <span style="color: red;">Lora weights file and folder </span>in the code.
