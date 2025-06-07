---
date: '2025-06-06T22:23:27+01:00'
draft: false
title: 'SD3.5 ControlNet Pitfall'
---
Bash prompt for training a ControlNet on Stable Diffusion v3.5
```bash
export MODEL_DIR="stabilityai/stable-diffusion-3.5-medium"
export OUTPUT_DIR="sd3-controlnet-out"

accelerate launch train_controlnet_sd3.py \
    --pretrained_model_name_or_path=$MODEL_DIR \
    --output_dir=$OUTPUT_DIR \
    --dataset_name=""fusing/fill50k" \
    --resolution=1024 \
    --learning_rate=1e-5 \
    --max_train_steps=15000 \
    --validation_image "./conditioning_image_1.png" "./conditioning_image_2.png" \
    --validation_prompt "red circle with blue background" "cyan circle with brown floral background" \
    --validation_steps=100 \
    --train_batch_size=1 \
    --gradient_accumulation_steps=4
```

Full tutorials should be referred in the "diffusers" github page:
https://github.com/huggingface/diffusers/blob/main/examples/controlnet/README_sd3.md
