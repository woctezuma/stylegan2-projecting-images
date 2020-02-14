# StyleGAN2: projecting images

The goal of this [Google Colab](https://colab.research.google.com/) notebook is to project images to latent space with StyleGAN2.

## Usage

-   Run `stylegan2_projecting_images.ipynb`.

## Results

I have downloaded a picture of the French president Emmanuel Macron, then center-cropped it to 1024x1024 respolution, and finally projected it to the latent space of the StyleGAN2 model trained on the FFHQ dataset with configuration f.

<img alt="Target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-target.png" width="250"><img alt="Projected image n°1/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step0200.png" width="250"><img alt="Projected image n°5/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step1000.png" width="250">

## References

-   [StyleGAN2](https://github.com/NVlabs/stylegan2)
