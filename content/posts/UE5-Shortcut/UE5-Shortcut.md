---
date: '2025-06-22T12:35:38+01:00'
draft: false
title: 'UE5 Shortcut'
---
```alt + [ ```enlarge the gizmo scale, ```alt + ]``` vice versa.

```Ctrl+B``` browse the asset

```Ctrl + E``` browse the material

```alt + P``` : Play game

```shift + F1```: Get mouse back while playing the game

Left top button click -> Advanced setting -> camera preview -> uncheck display camera preview window

### Recording in UE5
1. Adding a CineCameraActor
2. Take recorder
3. Change the view from perspective to CineCameraActor
4. Add source from actor->CineCameraActor
5. Click red button start to record
6. Now move the view in the viewport as the camera moves correspondingly
7. Finish recording by clicking stop button on the right bottom, or click the red button
8. Browse the content drawer, find the cinematics -> Takes -> [Date] and find the recording
9. Double click or drag it to the sequencer
10. Render the film

### Seqencer in UE5
1. Add a new levelsequencer by clicking the third button on the top. 
2. Add a track -> Actor to Sequencer -> CineCameraActor
3. Adding key frame to the CineCameraActor
4. Render the film