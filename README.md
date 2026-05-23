# Intern Challenge: From Video to 3D Reconstruction

## Approach

The notebook uses an off-the-shelf high-quality reconstruction stack:

1. Prepare image observations from either the included example scene or a user video.
2. Use camera poses and sparse geometry in Nerfstudio format.
3. Train a Gaussian Splatting model with `splatfacto-big`.
4. Export the trained scene as a `.ply` Gaussian Splat.
5. View the result interactively in SuperSplat.

For the included demo, the notebook downloads Nerfstudio's public `poster`
dataset from Hugging Face. This scene is useful for the challenge because it is
small, indoor, textured, and has reliable camera poses.

## Recommended Runtime

I recommend running the notebook in **Google Colab with a T4 GPU**.

With the default setting of **15,000 training iterations**, the full training run
usually takes about **40 minutes on a T4**. Runtime can vary depending on Colab
load and whether CUDA extensions need to be compiled during the first run.


## Main File

Open and run:

```text
video_to_3D.ipynb
```
in google colab

The notebook is organized into three main stages:

- configuration and data preparation
- Gaussian Splatting training
- export of the final `.ply` file

## How To Run

1. Open the notebook in Google Colab.
2. Select a GPU runtime:
   - `Runtime` -> `Change runtime type` -> `T4 GPU`
3. Run the setup/install cells if they are not already included in the Colab session.
4. Leave `VIDEO_PATH` empty to run the default indoor poster example.
5. Keep the default quality setting:

```python
METHOD_NAME = "splatfacto-big"
MAX_ITERATIONS = 15000
```

6. Run all cells in order.

During training, logs are written to:

```text
/content/video2scene_colab/train.log
```

After training finishes, the export cell prints the output folder path. It will
look similar to:

```text
/content/video2scene_colab/exports/splat_output
```

Go to that folder in the Colab file browser and download the exported `.ply`
file.

## Viewing The Result

The simplest way to inspect the reconstruction is:

1. Open https://superspl.at/editor
2. Drag and drop the downloaded `.ply` file into the page.
3. Navigate around the scene interactively.

This gives a quick visual check of geometry, camera coverage, floating points,
and overall reconstruction quality.

## Example Input And Output

Default input:

- Nerfstudio `poster` indoor scene
- downloaded automatically from Hugging Face
- uses image observations plus camera poses

Output:

- Gaussian Splat `.ply` file
- interactive 3D scene viewable in SuperSplat

The result should show the main room structure and visible objects, including
the chair and poster

## Notes On Custom Videos

For a custom phone video, the reconstruction quality depends heavily on camera
motion and pose estimation.

Gaussian Splatting still needs camera poses. For an arbitrary video, the frames
should first be processed into a Nerfstudio-compatible dataset with camera
poses, for example using Nerfstudio/COLMAP preprocessing. Once poses are
available, the same Splatfacto training and export steps can be used.

## What Can Be Improved

The largest remaining visual issue is usually noise: small floating splats can
appear in empty space, making the air look partially reconstructed.

Possible improvements:

- remove low-opacity Gaussians and very large or unstable splats
- train longer or tune pruning/culling thresholds
- capture cleaner video with more parallax and less blur
- use higher-resolution frames if GPU memory allows

Noise removal is the most practical next improvement because the main geometry
is already reconstructed well, while many remaining artifacts are isolated
floating points rather than missing structure.

## Design Tradeoffs

This solution prioritizes reconstruction quality and usability over implementing
all geometry algorithms from scratch. Nerfstudio Splatfacto provides a strong
modern Gaussian Splatting baseline, while Colab makes GPU training accessible
even without a local CUDA machine.

The tradeoff is that the best-quality path depends on GPU execution and on
reliable camera poses. In return, the final result is easy to train, export, and
view interactively.
