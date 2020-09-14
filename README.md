# StyleGAN2: projecting images

The goal of this [Google Colab](https://colab.research.google.com/) notebook is to project images to latent space with StyleGAN2.

## Usage

To discover how to project a real image using the original StyleGAN2 implementation, run:
-   [`stylegan2_projecting_images.ipynb`][stylegan2_projecting_images]

To process the projection of **a batch** of images, using either `W(1,*)` (original) or `W(18,*)` (extended), run:
-   [`stylegan2_projecting_images_with_my_fork.ipynb`][stylegan2_projecting_images_with_fork].

For more information about `W(1,*)` and `W(18,*)`, please refer to the [the original paper][stylegan2-paper] (section 5 on page 7):

> Inverting the synthesis network $g$ is an interesting problem that has many applications.
> Manipulating a given image in the latent feature space requires finding a matching latent code $w$ for it first.

The following is about `W(18,*)`:
> Previous research suggests that instead of finding a common latent code $w$, the results improve if a separate $w$ is chosen for each layer of the generator.
> The same approach was used in an early encoder implementation.

The following is about `W(1,*)`, which is the approach used in the original implementation:
> While extending the latent space in this fashion finds a closer match to a given image, it also enables projecting arbitrary images that should have no latent representation.
> Instead, we concentrate on finding latent codes in the original, unextended latent space, as these correspond to images that the generator could have produced.

## Data

Data consists of:
-   1 picture of the French president Emmanuel Macron, found on [Nice Matin][french-president] ([archive][french-president-archive]),
-   37 individual pictures of the French government, found on [Wikipedia][french-government] ([list][french-government-archive]),
-   5 pictures of famous paintings, found on Wikipedia ([list][famous-paintings-archive]):
    - [La Joconde](https://fr.wikipedia.org/wiki/La_Joconde), 
    - [Le Condottière](https://fr.wikipedia.org/wiki/Le_Condotti%C3%A8re_(Antonello_de_Messine)),
    - [La Naissance de Vénus](https://fr.wikipedia.org/wiki/La_Naissance_de_V%C3%A9nus_(Botticelli)),
    - [Ginevra de' Benci](https://fr.wikipedia.org/wiki/Portrait_de_Ginevra_de%27_Benci),
    - [La Jeune Fille à la perle](https://fr.wikipedia.org/wiki/La_Jeune_Fille_%C3%A0_la_perle).

<img alt="Original image of the French president" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/emmanuel-macron.jpg" width="250">

## Pre-processing

There are two possible pre-processing methods:
-   either center-cropping (to 1024x1024 resolution) as sole pre-processing,

<img alt="Center-cropping" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/emmanuel-macron-crop.jpg" width="250">

-   or the same pre-processing as for the [FFHQ dataset]:
    1. first, an alignment based on 68 face landmarks returned by [dlib],
    2. then reproduce `recreate_aligned_images()`, as detailed in [FFHQ pre-processing code].

<img alt="Face landmarks" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/face_landmarks/landmarks_macron.jpg" width="250">

<img alt="FFHQ pre-processing" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/emmanuel-macron-aligned.jpg" width="250">

Finally, the pre-processed image can be projected to the latent space of the StyleGAN2 model trained with configuration f on the Flickr-Faces-HQ (FFHQ) dataset.

## Results

NB: results are different if the code is run twice, even if the same pre-processing is used.

### With center-cropping as sole pre-processing

The result below is obtained with center-cropping as sole pre-processing, hence some issues with the projection.

![Projection (with issues) as GIF](https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/gif/movie0000-opt.gif)

From left to right: the target image, the result obtained at the start of the projection, and the final result of the projection.

<img alt="Target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-target.jpg" width="250"><img alt="Projected image n°1/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step0200.jpg" width="250"><img alt="Projected image n°5/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00000-project-real-images/image0000-step1000.jpg" width="250">

From left to right: the target image, the result obtained at the start of the projection, intermediate results, and the final result.

![Projection results (with issues) as PNG](https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/result0000.jpg)

The background, the hair, the ears, and the suit are relatively well reproduced, but the face is wrong, especially the neck (in the original image) is confused with the chin (in the projected images).
It is possible that the face is too small relatively to the rest of the image, compared to the FFHQ training dataset, hence the poor results of the projection.

### With the same pre-processing as for the FFHQ dataset

The result below is obtained with the same pre-processing as for the FFHQ dataset, which allows to avoid the projection issues mentioned above.

![Projection (without issues) as GIF](https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/gif/movie0001-opt.gif)

From left to right: the target image, the result obtained at the start of the projection, and the final result of the projection.

<img alt="Target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00001-project-real-images/image0001-target.jpg" width="250"><img alt="Projected image n°1/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00001-project-real-images/image0001-step0200.jpg" width="250"><img alt="Projected image n°5/5" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/00001-project-real-images/image0001-step1000.jpg" width="250">

From left to right: the target image, the result obtained at the start of the projection, intermediate results, and the final result.

![Projection results as PNG](https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/result0001.jpg)

### Additional projection results

Additional projection results are shared on [Google Drive][additional-projection-results].

The directory structure is as follows:
```
stylegan2_projections/
├ aligned_images/
├ └ emmanuel-macron_01.png    # FFHQ-aligned image
├ generated_images_no_tiled/  # projections with `W(18,*)`
├ ├ emmanuel-macron_01.npy    # - latent code
├ └ emmanuel-macron_01.png    # - projected image
├ generated_images_tiled/     # projections with `W(1,*)`
├ ├ emmanuel-macron_01.npy    # - latent code
├ └ emmanuel-macron_01.png    # - projected image
├ aligned_images.tar.gz             # folder archive
├ generated_images_no_tiled.tar.gz  # folder archive
└ generated_images_tiled.tar.gz     # folder archive
```

#### Extended projection

The extended projection allows to obtain an image closer to the target image, [at the expense of semantics][extended-projection-limitations].

##### French politicians

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/09.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/42.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/32.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/09.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/42.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/32.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/09.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/42.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/32.jpg" width="250">

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/27.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/07.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/38.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/27.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/07.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/38.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/27.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/07.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/38.jpg" width="250">

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/08.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/22.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/33.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/08.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/22.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/33.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/08.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/22.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/33.jpg" width="250">

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/05.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/18.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/30.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/05.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/18.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/30.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/05.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/18.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/30.jpg" width="250">

##### Art

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/29.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/28.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/41.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/29.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/28.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/41.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/29.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/28.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/41.jpg" width="250">

From top to bottom: aligned target image, projection with `W(1,*)`, projection with `W(18,*)`.

<img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/02.jpg" width="250"><img alt="Aligned target image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/aligned_images/06.jpg" width="250">

<img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/02.jpg" width="250"><img alt="W1 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_tiled/06.jpg" width="250">

<img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/02.jpg" width="250"><img alt="W18 projected image" src="https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/results/generated_images_no_tiled/06.jpg" width="250">

## References

-   StyleGAN2:
    -   [StyleGAN2](https://github.com/NVlabs/stylegan2)
    -   My [fork][stylegan2-fork] of StyleGAN2 to project **a batch** of images, using either `W(1,*)` or `W(18,*)`
    -   [Steam-StyleGAN2](https://github.com/woctezuma/steam-stylegan2)
-   [rolux/stylegan2encoder](https://github.com/rolux/stylegan2encoder): align faces based on detected landmarks (same as FFHQ pre-processing).
-   [rosasalberto/StyleGAN2-TensorFlow-2.x][rosasalberto-fork]: tutorial notebooks to [generate][rosasalberto-sample-from-latents] from latent vectors and [edit][rosasalberto-edit-latents] them.
-   Minimal example [Gist][minimal-example-latent-edition] to edit latent vectors.
-   Learnt [latent directions](https://github.com/a312863063/generators-with-stylegan2) for StyleGAN2
-   Colab [user interface](https://github.com/tg-bomze/StyleGAN2-Face-Modificator) for projection and face modification along latent directions
-   A blog post about editing projected images to to add a [cartoon][toonify-blog-post] effect
-   Interesting external tools:
    -   On my Wiki: [GIF editing][wiki-gif-editing] with [MoviePy][moviepy] and [Gifsicle][gifsicle]
    -   [ArtBreeder](https://artbreeder.com/) by Joel Simon
-   Papers loosely relevant to image projection:
    - [Karras, T., Laine, S., Aittala, M., Hellsten, J., Lehtinen, J., & Aila, T. (2020). Analyzing and improving the image quality of stylegan. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (pp. 8110-8119).][stylegan2-paper]
    - [Abdal, R., Qin, Y., & Wonka, P. (2019). Image2stylegan: How to embed images into the stylegan latent space?. In Proceedings of the IEEE international conference on computer vision (pp. 4432-4441).][image2stylegan-paper]
    - [Karras, T., Laine, S., & Aila, T. (2019). A style-based generator architecture for generative adversarial networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4401-4410).][stylegan1-paper]
    - [Zhang, R., Isola, P., Efros, A. A., Shechtman, E., & Wang, O. (2018). The unreasonable effectiveness of deep features as a perceptual metric. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 586-595).][lpips-paper]
-   Papers about discovering latent directions:
    - [Shen, Y., Gu, J., Tang, X., & Zhou, B. (2019). Interpreting the latent space of gans for semantic face editing. arXiv preprint arXiv:1907.10786.][interfacegan]
    - [Härkönen, E., Hertzmann, A., Lehtinen, J., & Paris, S. (2020). GANSpace: Discovering Interpretable GAN Controls. arXiv preprint arXiv:2004.02546.][ganspace]
    - [Pidhorskyi, S., Adjeroh, D., & Doretto, G. (2020). Adversarial Latent Autoencoders. arXiv preprint arXiv:2004.04467.][ALAE]
    - [Shen, Y., & Zhou, B. (2020). Closed-Form Factorization of Latent Semantics in GANs. arXiv preprint arXiv:2007.06600.][closed-form]

<!-- Definitions -->

[french-president]: <https://cdn.static01.nicematin.com/media/npo/1440w/2017/06/emmanuel-macron.jpg>
[french-president-archive]: <https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/emmanuel-macron.jpg>
[french-government]: <https://fr.wikipedia.org/wiki/Gouvernement_%C3%89douard_Philippe_(2)#Galerie_du_gouvernement_actuel>
[french-government-archive]: <https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/french-government-links.txt>
[famous-paintings-archive]: <https://raw.githubusercontent.com/wiki/woctezuma/stylegan2-projecting-images/img/famous-paintings-links.txt>
[FFHQ dataset]: <https://github.com/NVlabs/ffhq-dataset>
[dlib]: <http://dlib.net/face_landmark_detection.py.html>
[FFHQ pre-processing code]: <https://github.com/NVlabs/ffhq-dataset/blob/master/download_ffhq.py>

[stylegan2_projecting_images]: <https://colab.research.google.com/github/woctezuma/stylegan2-projecting-images/blob/master/stylegan2_projecting_images.ipynb>
[stylegan2_projecting_images_with_fork]: <https://colab.research.google.com/github/woctezuma/stylegan2-projecting-images/blob/master/stylegan2_projecting_images_with_my_fork.ipynb>

[lpips-paper]: <https://arxiv.org/abs/1801.03924>
[image2stylegan-paper]: <https://arxiv.org/abs/1904.03189>
[stylegan1-paper]: <https://arxiv.org/abs/1812.04948>
[stylegan2-paper]: <https://arxiv.org/abs/1912.04958>
[stylegan2-fork]: <https://github.com/woctezuma/stylegan2/tree/tiled-projector>

[additional-projection-results]: <https://drive.google.com/drive/folders/1-3SUTqK5RpSHgCjKaDKKGpJdkB7iz2VZ?usp=sharing>
[extended-projection-limitations]: <https://github.com/rolux/stylegan2encoder/issues/21>
[minimal-example-latent-edition]: <https://gist.github.com/woctezuma/139cedb92a94c5ef2675cc9f06851b31>

[wiki-gif-editing]: <https://github.com/woctezuma/stylegan2-projecting-images/wiki/README>
[moviepy]: <https://github.com/Zulko/moviepy>
[gifsicle]: <https://github.com/kohler/gifsicle>
[toonify-blog-post]: <https://www.justinpinkney.com/toonify-yourself/>

[interfacegan]: <https://github.com/genforce/interfacegan>
[ganspace]: <https://github.com/harskish/ganspace>
[ALAE]: <https://github.com/podgorskiy/ALAE>
[closed-form]: <https://github.com/rosinality/stylegan2-pytorch#closed-form-factorization-httpsarxivorgabs200706600>

[rosasalberto-fork]: <https://github.com/rosasalberto/StyleGAN2-TensorFlow-2.x>
[rosasalberto-sample-from-latents]: <https://github.com/rosasalberto/StyleGAN2-TensorFlow-2.x/blob/master/example_how_to_use.ipynb>
[rosasalberto-edit-latents]: <https://github.com/rosasalberto/StyleGAN2-TensorFlow-2.x/blob/master/example_latent_changes.ipynb>
