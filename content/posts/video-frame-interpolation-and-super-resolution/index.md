---
date: '2026-01-26T20:21:50Z'
draft: false
title: 'Video Frame Interpolation and Super Resolution'
---

# Video Frame Interpolation
Here I used Practical_RIFE on Github. Official website:https://github.com/hzwer/Practical-RIFE

If it reports the error of ffmpeg while running video_interpolate.py, just install the ffmpeg in the official website. A latest version of essential contents zip file will be sufficient.

My case is to interpolate frames for a wmv file. WMV file is an old video file type, which could lead to false detection of fps and other parameters by RIFE. Hence, to transfer to mp4 normally is necessary.

## Video preprocessing

### For the highest level of detail maintenance (transfer from wmv to mp4)
```bash
ffmpeg -hwaccel cuda -i xxx.wmv -c:v h264_nvenc -preset p7 -rc constqp -qp 12 -pix_fmt yuv420p -color_range pc -colorspace bt709 -c:a aac -b:a 192k input_ultra.mp4
```

### Remove the duplicated frames and synchronize the video and audio
```bash
ffmpeg -hwaccel cuda -i input_ultra.mp4 -vf "mpdecimate,fps=13.88" -c:v h264_nvenc -preset p7 -qp 12 -c:a aac -b:a 192k input_sync.mp4
```

### If the video still suffers from unsmooth frame transitions, remake the timeline so that all the frames are evenly seperated on the timeline.
```bash
ffmpeg -hwaccel cuda -i input_sync.mp4 -vf "fps=15" -c:v h264_nvenc -preset p7 -qp 12 -af "aresample=async=1" input_15fps_raw.mp4
```
Originally the fps was 13.8xxx, which was a irregular value. This command changes the video to fps=15, but keeps the synchronization of video & audio.

## AI Interpolation
```bash
python inference_video.py --video=input_15fps_raw.mp4 --multi=4
```
If the camera motion is evident, GMFSS could be a more powerful AI tool for interpolation.
Official website: https://github.com/98mxr/GMFSS_Fortuna
# Video graphic super-resolution
For 3D real video/images, it is recommended to use Real_ESRGAN.
Official website: https://github.com/xinntao/Real-ESRGAN

If ffmpeg not installed error reported, even if you installed ffmpeg globally, do ```pip install ffmpeg``` in the environment.

Remember to assign a model_name when running the inference.
```bash
python inference_realesrgan_video.py --model_name=realesr-animevideov3 --input=./inputs/video/xxx.mp4
```