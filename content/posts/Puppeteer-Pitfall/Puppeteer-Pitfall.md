---
date: '2025-12-31T19:48:21Z'
draft: false
title: 'Puppeteer Pitfall'
---

Tested environment:
CUDA==12.1 python==3.10.3 pytorch==2.1.1

```bash
git clone https://github.com/Seed3D/Puppeteer.git --recursive && cd Puppeteer
pip install cython==0.29.36
pip install -r requirements.txt
pip install flash-attn==2.6.3 --no-build-isolation
pip install torch-scatter -f https://data.pyg.org/whl/torch-2.1.1+cu121.html
pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py310_cu121_pyt211/downlo
ad.html

apt-get update
apt-get install -y libgl1 libglib2.0-0 libegl1 libxrender1 libxext6 libx11-6 libglu1-mesa libglvnd0 libgl1-mesa-glx libgl1-mesa-dri
```

Download weights in skeleton/, skinning/, animation/, run:
```bash
python download
```

In skinning/third_parties:
```bash
ln -s ../../skeleton/third_partys/Michelangelo/ ./
```

move skinning_ckpts, skeleton_ckpts one level upward:
```bash
cd skinning/skinning
mv skinning_ckpts ../
cd ../..
cd skeleton/skeleton
mv skeleton_ckpts ../
```