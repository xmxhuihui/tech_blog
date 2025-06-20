---
date: '2025-06-20T13:33:23+01:00'
draft: false
title: 'Hunyuan3D-2.1 Pitfall'
---
Hunyuan3D-2.1加入了对PBR material的支持，以下是我在尝试该项目时遇到的问题和解决方法的总结。

Troubleshooting:
```bash
ImportError: libSM.so.6: cannot open shared object file: No such file or directory
```
Ubuntu系统上的解决方案：
```bash
sudo apt update
sudo apt install libsm6 libxrender1 libxext6
```
Other install packages
```bash
pip install sentencepiece
# download the hy3dgen for txt2img from Hunyuan3D-2
```

分享一下改进后的img-to-3D demo.py
```python
import sys
sys.path.insert(0, './hy3dshape')
sys.path.insert(0, './hy3dpaint')

from PIL import Image
from hy3dshape.rembg import BackgroundRemover
from hy3dshape.pipelines import Hunyuan3DDiTFlowMatchingPipeline
import os

from textureGenPipeline import Hunyuan3DPaintPipeline, Hunyuan3DPaintConfig

try:
    from torchvision_fix import apply_fix
    apply_fix()
except ImportError:
    print("Warning: torchvision_fix module not found, proceeding without compatibility fix")                                      
except Exception as e:
    print(f"Warning: Failed to apply torchvision fix: {e}")

# shape
model_path = 'tencent/Hunyuan3D-2.1'
pipeline_shapegen = Hunyuan3DDiTFlowMatchingPipeline.from_pretrained(model_path)

image_path = 'assets/example_images/mmexportde235211f304fd83ec9d60e38c9f6f0b_1747975667632.png'
filename = os.path.basename(image_path).split('.')[0]
image = Image.open(image_path).convert("RGBA")
if image.mode == 'RGB':
    rembg = BackgroundRemover()
    image = rembg(image)
save_folder = os.path.join('output', filename)
os.makedirs(save_folder, exist_ok=True)
mesh = pipeline_shapegen(image=image)[0]

mesh.export(os.path.join(output_folder, filename + '.glb'))

# paint
max_num_view = 6  # can be 6 to 9
resolution = 512  # can be 768 or 512
conf = Hunyuan3DPaintConfig(max_num_view, resolution)
conf.realesrgan_ckpt_path = "hy3dpaint/ckpt/RealESRGAN_x4plus.pth"
conf.multiview_cfg_path = "hy3dpaint/cfgs/hunyuan-paint-pbr.yaml"
conf.custom_pipeline = "hy3dpaint/hunyuanpaintpbr"
paint_pipeline = Hunyuan3DPaintPipeline(conf)

output_mesh_path = filename + '_textured.glb'
output_mesh_path = paint_pipeline(
    mesh_path = os.path.join(output_folder, filename + '.glb'), 
    image_path = image_path,
    output_mesh_path = os.path.join(output_folder, output_mesh_path)
)
```

以及txt-to-3D_demo.py
```python
import sys
sys.path.insert(0, './hy3dshape')
sys.path.insert(0, './hy3dpaint')

from PIL import Image
from hy3dshape.rembg import BackgroundRemover
from hy3dshape.pipelines import Hunyuan3DDiTFlowMatchingPipeline
import os
import time
import torch
from hy3dshape.utils import logger
from hy3dpaint.convert_utils import create_glb_with_pbr_materials
from gradio_app import export_mesh, randomize_seed_fn, quick_convert_with_obj2gltf
from hy3dgen.text2image import HunyuanDiTPipeline
from textureGenPipeline import Hunyuan3DPaintPipeline, Hunyuan3DPaintConfig
from hy3dshape.pipelines import export_to_trimesh
from hy3dshape import FaceReducer, FloaterRemover, DegenerateFaceRemover, MeshSimplifier, \
        Hunyuan3DDiTFlowMatchingPipeline
try:
    from torchvision_fix import apply_fix
    apply_fix()
except ImportError:
    print("Warning: torchvision_fix module not found, proceeding without compatibility fix")                                      
except Exception as e:
    print(f"Warning: Failed to apply torchvision fix: {e}")
    
def _gen_shape(
    caption=None,
    image=None,
    mv_image_front=None,
    mv_image_back=None,
    mv_image_left=None,
    mv_image_right=None,
    steps=50,
    guidance_scale=7.5,
    seed=1234,
    octree_resolution=256,
    check_box_rembg=False,
    num_chunks=200000,
    randomize_seed: bool = False,
):
    # if not MV_MODE and image is None and caption is None:
    #     raise gr.Error("Please provide either a caption or an image.")
    # if MV_MODE:
    #     if mv_image_front is None and mv_image_back is None \
    #         and mv_image_left is None and mv_image_right is None:
    #         raise gr.Error("Please provide at least one view image.")
        # image = {}
        # if mv_image_front:
        #     image['front'] = mv_image_front
        # if mv_image_back:
        #     image['back'] = mv_image_back
        # if mv_image_left:
        #     image['left'] = mv_image_left
        # if mv_image_right:
        #     image['right'] = mv_image_right

    seed = int(randomize_seed_fn(seed, randomize_seed))

    octree_resolution = int(octree_resolution)
    if caption: print('prompt is', caption)
    # save_folder = gen_save_folder()
    # stats = {
    #     'model': {
    #         'shapegen': f'{args.model_path}/{args.subfolder}',
    #         'texgen': f'{args.texgen_model_path}',
    #     },
    #     'params': {
    #         'caption': caption,
    #         'steps': steps,
    #         'guidance_scale': guidance_scale,
    #         'seed': seed,
    #         'octree_resolution': octree_resolution,
    #         'check_box_rembg': check_box_rembg,
    #         'num_chunks': num_chunks,
    #     }
    # }
    time_meta = {}
    
    if image is None:
        start_time = time.time()
        try:
            t2i_worker = HunyuanDiTPipeline('Tencent-Hunyuan/HunyuanDiT-v1.1-Diffusers-Distilled')
            image = t2i_worker(caption)
        except Exception as e:
            raise Exception(f"Text to 3D is disable. \
            Please enable it by `python gradio_app.py --enable_t23d`.")
        time_meta['text2image'] = time.time() - start_time

    # remove disk io to make responding faster, uncomment at your will.
    # image.save(os.path.join(save_folder, 'input.png'))
    rmbg_worker = BackgroundRemover()
    if MV_MODE:
        start_time = time.time()
        for k, v in image.items():
            if check_box_rembg or v.mode == "RGB":
                img = rmbg_worker(v.convert('RGB'))
                image[k] = img
        time_meta['remove background'] = time.time() - start_time
    else:
        if check_box_rembg or image.mode == "RGB":
            start_time = time.time()
            image = rmbg_worker(image.convert('RGB'))
            time_meta['remove background'] = time.time() - start_time

    # remove disk io to make responding faster, uncomment at your will.
    # image.save(os.path.join(save_folder, 'rembg.png'))

    # image to white model
    start_time = time.time()

    generator = torch.Generator()
    generator = generator.manual_seed(int(seed))
    i23d_worker = Hunyuan3DDiTFlowMatchingPipeline.from_pretrained(
        args.model_path,
        subfolder=args.subfolder,
        use_safetensors=False,
        device=args.device,
    )
    outputs = i23d_worker(
        image=image,
        num_inference_steps=steps,
        guidance_scale=guidance_scale,
        generator=generator,
        octree_resolution=octree_resolution,
        num_chunks=num_chunks,
        output_type='mesh'
    )
    time_meta['shape generation'] = time.time() - start_time
    logger.info("---Shape generation takes %s seconds ---" % (time.time() - start_time))

    tmp_start = time.time()
    mesh = export_to_trimesh(outputs)[0]
    time_meta['export to trimesh'] = time.time() - tmp_start

    # stats['number_of_faces'] = mesh.faces.shape[0]
    # stats['number_of_vertices'] = mesh.vertices.shape[0]

    # stats['time'] = time_meta
    main_image = image if not MV_MODE else image['front']
    return mesh, main_image, seed
def generation_all(
    caption=None,
    image=None,
    mv_image_front=None,
    mv_image_back=None,
    mv_image_left=None,
    mv_image_right=None,
    steps=50,
    guidance_scale=7.5,
    seed=1234,
    octree_resolution=256,
    check_box_rembg=False,
    num_chunks=200000,
    randomize_seed: bool = False,
):
    start_time_0 = time.time()
    mesh, image, seed = _gen_shape(
        caption,
        image,
        mv_image_front=mv_image_front,
        mv_image_back=mv_image_back,
        mv_image_left=mv_image_left,
        mv_image_right=mv_image_right,
        steps=steps,
        guidance_scale=guidance_scale,
        seed=seed,
        octree_resolution=octree_resolution,
        check_box_rembg=check_box_rembg,
        num_chunks=num_chunks,
        randomize_seed=randomize_seed,
    )
    save_folder = os.path.join('output', '_'.join(caption.split()))
    os.makedirs(save_folder, exist_ok=True)
    path = export_mesh(mesh, save_folder, textured=False)
    

    print(path)
    print('='*40)

    # tmp_time = time.time()
    # mesh = floater_remove_worker(mesh)
    # mesh = degenerate_face_remove_worker(mesh)
    # logger.info("---Postprocessing takes %s seconds ---" % (time.time() - tmp_time))
    # stats['time']['postprocessing'] = time.time() - tmp_time

    tmp_time = time.time()
    face_reduce_worker = FaceReducer()
    mesh = face_reduce_worker(mesh)

    # path = export_mesh(mesh, save_folder, textured=False, type='glb')
    path = export_mesh(mesh, save_folder, textured=False, type='obj') # 这样操作也会 core dump

    logger.info("---Face Reduction takes %s seconds ---" % (time.time() - tmp_time))
    # stats['time']['face reduction'] = time.time() - tmp_time

    tmp_time = time.time()

    text_path = os.path.join(save_folder, f'textured_mesh.obj')
    
    conf = Hunyuan3DPaintConfig(max_num_view=8, resolution=768)
    conf.realesrgan_ckpt_path = "hy3dpaint/ckpt/RealESRGAN_x4plus.pth"
    conf.multiview_cfg_path = "hy3dpaint/cfgs/hunyuan-paint-pbr.yaml"
    conf.custom_pipeline = "hy3dpaint/hunyuanpaintpbr"
    tex_pipeline = Hunyuan3DPaintPipeline(conf)
    
    path_textured = tex_pipeline(mesh_path=path, image_path=image, output_mesh_path=text_path, save_glb=False)
        
    logger.info("---Texture Generation takes %s seconds ---" % (time.time() - tmp_time))
    # stats['time']['texture generation'] = time.time() - tmp_time

    tmp_time = time.time()
    # Convert textured OBJ to GLB using obj2gltf with PBR support
    glb_path_textured = os.path.join(save_folder, 'textured_mesh.glb')
    conversion_success = quick_convert_with_obj2gltf(path_textured, glb_path_textured)

    logger.info("---Convert textured OBJ to GLB takes %s seconds ---" % (time.time() - tmp_time))
    # stats['time']['convert textured OBJ to GLB'] = time.time() - tmp_time
    # stats['time']['total'] = time.time() - start_time_0
    # model_viewer_html_textured = build_model_viewer_html(save_folder, 
    #                                                      height=HTML_HEIGHT, 
    #                                                      width=HTML_WIDTH, textured=True)
    if args.low_vram_mode:
        torch.cuda.empty_cache()
    # return (
    #     gr.update(value=path),
    #     gr.update(value=glb_path_textured),
    #     model_viewer_html_textured,
    #     stats,
    #     seed,
    # )
    
if __name__=="__main__":
    import argparse

    parser = argparse.ArgumentParser()
    parser.add_argument("--model_path", type=str, default='tencent/Hunyuan3D-2.1')
    parser.add_argument("--subfolder", type=str, default='hunyuan3d-dit-v2-1')
    parser.add_argument("--texgen_model_path", type=str, default='tencent/Hunyuan3D-2.1')
    parser.add_argument('--caption', type=str, default="a lovely rabbit eating carrots")
    parser.add_argument('--device', type=str, default='cuda')
    parser.add_argument('--low_vram_mode', action='store_true')
    args = parser.parse_args()
    caption = args.caption
    MV_MODE = False
    generation_all(caption=caption)
```