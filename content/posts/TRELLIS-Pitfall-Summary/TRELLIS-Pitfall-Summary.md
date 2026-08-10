---
date: '2026-03-16T22:39:57Z'
draft: true
title: 'TRELLIS Pitfall Summary'
---
# Environment installation
## trellis
```bash
. ./setup.sh --basic --xformers --flash-attn --diffoctreerast --spconv --mipgaussian --kaolin --nvdiffrast	
pip install wandb
pip install kaolin -f https://nvidia-kaolin.s3.us-east-2.amazonaws.com/torch-2.4.0_cu118.html
pip install tensorboard pandas lpips
pip install h5py
pip install smplx[all]
pip uninstall open3d-python open3d
pip install open3d
pip install multimethod==1.7
pip install shortuuid
pip install xformers==0.0.27.post2 --index-url https://download.pytorch.org/whl/cu118

# diffoctreerast installation
conda install -c conda-forge glm # on NCC where sudo does not work.
ls $CONDA_PREFIX/include/glm
export CPLUS_INCLUDE_PATH=$CONDA_PREFIX/include:$CPLUS_INCLUDE_PATH
export CPATH=$CONDA_PREFIX/include:$CPATH
. ./setup.sh --diffoctreerast
```
## pointnext
```bash
cd openpoints/cpp/pointnet2_batch
python setup.py install
cd ../
# Blow are functions that optional. Necessary only if interested in reconstruction tasks such as completion
cd chamfer_dist
python setup.py install --user
cd ../emd
python setup.py install --user
cd ../../../

pip install peft
```