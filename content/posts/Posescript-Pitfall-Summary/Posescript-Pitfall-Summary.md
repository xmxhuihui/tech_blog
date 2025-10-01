---
date: '2025-10-01T10:23:55+01:00'
draft: false
title: 'Posescript Pitfall Summary'
---
Pytorch version:
```bash
torch                     2.2.0+cu121              pypi_0    pypi
torchaudio                2.2.0+cu121              pypi_0    pypi
torchdata                 0.7.1                    pypi_0    pypi
torchtext                 0.17.0                   pypi_0    pypi
torchvision               0.17.0+cu121             pypi_0    pypi
```
data文件夹结构
```
data
 -AMASS
  -smplh
   -ACCAD
   -BioMotionLab_NTroje
   -...
 -PoseScript
  -posescript_release
 -smplh-amass-body-models
  -female
  -male
  -neutral
```
Download gen_distilbert_dataPSA2ftPSH2 from the following command and put into ```src/text2pose/experiments```
```bash
wget https://download.europe.naverlabs.com/ComputerVision/PoseFix/gen_distilbert_dataPSA2ftPSH2.zip
```
```src/text2pose/encoders/text_encoder.py```替换117行和263行
```python
# self.pretrained_text_encoder = AutoModel.from_pretrained(os.path.join(config.TRANSFORMER_CACHE_DIR, "distilbert-base-uncased"))
self.pretrained_text_encoder = AutoModel.from_pretrained("distilbert-base-uncased", cache_dir="/home/zhefei/Documents/Projects/posescript/tools/huggingface_models")
# self.pretrained_text_encoder = CLIPTextModelWithProjection.from_pretrained(os.path.join(config.TRANSFORMER_CACHE_DIR, 'openai/clip-vit-base-patch32'))
self.pretrained_text_encoder = CLIPTextModelWithProjection.from_pretrained('openai/clip-vit-base-patch32', cache_dir="/home/zhefei/Documents/Projects/posescript/tools/huggingface_models")
```
```src/text2pose/encoders/tokenizers.py```替换397行
```python
# self.tokenizer = AutoTokenizer.from_pretrained(os.path.join(config.TRANSFORMER_CACHE_DIR, "distilbert-base-uncased"))
self.tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased", cache_dir="/home/zhefei/Documents/Projects/posescript/tools/huggingface_models")
```
```tools/model_offline_download.py```用于离线下载模型，下载到```tools/```：
```python
from huggingface_hub import snapshot_download
import text2pose.config as config
import os
local_dir = snapshot_download(repo_id="distilbert-base-uncased", cache_dir=os.path.join(config.TRANSFORMER_CACHE_DIR, "distilbert-base-uncased"))
print(f"model downloaded to {local_dir}")
local_dir = snapshot_download(repo_id='openai/clip-vit-base-patch32', cache_dir=os.path.join(config.TRANSFORMER_CACHE_DIR, 'openai/clip-vit-base-patch32'))
print(f"model downloaded to {local_dir}")
```