---
date: '2026-05-11T20:53:51+01:00'
draft: false
title: 'Eval3D Pitfall Summary'
---
## Before Installation (Do only if <crypt.h> is not found (for some python environment))
```bash
export CC=/usr/bin/gcc
export CXX=/usr/bin/g++
export CPATH=/usr/include:/usr/include/x86_64-linux-gnu
```
## Installation
```bash
conda install -c conda-forge ninja cxx-compiler==1.3.0  

conda install pytorch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 pytorch-cuda=11.8 -c pytorch -c nvidia
# pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu118

pip install git+https://github.com/NVlabs/tiny-cuda-nn.git#subdirectory=bindings/torch  

pip install git+https://github.com/NVlabs/nvdiffrast.git --no-build-isolation

pip install -r requirements.txt 

conda install pytorch3d==0.7.5 -c pytorch3d
conda install -c conda-forge igl
```
requirements.txt:
```lightning==2.0.0
omegaconf==2.3.0
jaxtyping
typeguard
git+https://github.com/KAIR-BAIR/nerfacc.git@v0.5.2
diffusers<0.20
transformers==4.28.1
accelerate
opencv-python
open3d
tensorboard
matplotlib
imageio>=2.28.0
imageio[ffmpeg]
# git+https://github.com/NVlabs/nvdiffrast.git
# libigl
xatlas
trimesh[easy]
networkx
pysdf
pymcubes==0.1.4
pytorch-lightning==2.2.5
pyparsing
scikit-learn
wandb
gradio==4.11.0
git+https://github.com/ashawkey/envlight.git
git+https://github.com/mhamilton723/FeatUp
torchmetrics

# deepfloyd
bitsandbytes==0.38.1
sentencepiece
safetensors
huggingface_hub==0.25.0

# for zero123
einops
kornia
taming-transformers-rom1504
git+https://github.com/openai/CLIP.git

#controlnet
controlnet_aux

# dreamsim
dreamsim
```

Besides, to use DeepFloyd model, need to get access to the model in Huggingface and login in teminal.
https://huggingface.co/DeepFloyd/IF-I-XL-v1.0