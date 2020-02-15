# StyleGAN2: projecting images

The goal of this [Google Colab](https://colab.research.google.com/) notebook is to project images to latent space with StyleGAN2.

## Usage

-   Run `stylegan2_projecting_images.ipynb`.

## Results

I have downloaded a picture of the French president Emmanuel Macron, then center-cropped it to 1024x1024 resolution, and finally projected it to the latent space of the StyleGAN2 model trained with configuration f on the Flickr-Faces-HQ (FFHQ) dataset.

From left to right: the target image, the result obtained at the start of the projection, and the final result of the projection.

<img alt="Target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-target.png" width="250"><img alt="Projected image n°1/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step0200.png" width="250"><img alt="Projected image n°5/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step1000.png" width="250">

The background, the hair, the ears, and the suit are relatively well reproduced, but the face is wrong, especially the neck (in the original image) is confused with the chin (in the projected images).
It is possible that the face is too small relatively to the rest of the image, compared to the FFHQ training dataset, hence the poor results of the projection.

## References

-   [StyleGAN2](https://github.com/NVlabs/stylegan2)
-   [Steam-StyleGAN2](https://github.com/woctezuma/steam-stylegan2)
-   [My fork](https://github.com/woctezuma/stylegan2)
-   Interesting forks:
    - [skyflynil/stylegan2](https://github.com/skyflynil/stylegan2): automatically [resume](https://github.com/NVlabs/stylegan2/commit/8c57ee4633d334e480a23d7f82433c7649d50866) from the latest snapshot, allow non-square images, add vertical mirror augmentation,
    - [rolux/stylegan2encoder](https://github.com/rolux/stylegan2encoder): align faces based on detected landmarks (same as FFHQ pre-processing) then project to latent space,
    - [kreativai/stylegan2encoder](https://github.com/kreativai/stylegan2encoder): resume parameters input via CLI, automatic detection of the latest snapshot in the result folder,
    - [veqtor/stylegan2](https://github.com/veqtor/stylegan2): automatically resume from the latest snapshot,
    - [pbaylies/stylegan2](https://github.com/pbaylies/stylegan2): merge of some improvements listed above.
