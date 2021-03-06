## Fast Style Transfer in [TensorFlow](https://github.com/tensorflow/tensorflow)

Add famous painting styles to any photo in a fraction of a second! [You can even style videos!](#video-stylization)

<p align = 'center'>
<img src = 'examples/style/udnie.jpg' height = '246px'>
<img src = 'examples/content/stata.jpg' height = '246px'>
<a href = 'examples/results/stata_udnie.jpg'><img src = 'examples/results/stata_udnie_header.jpg' width = '627px'></a>
</p>
<p align = 'center'>
It takes 100ms on a 2015 Titan X to style the MIT Stata Center (1024×680) like Udnie, by Francis Picabia.
</p>

Our implementation is based off of a combination of Gatys' [A Neural Algorithm of Artistic Style](https://arxiv.org/abs/1508.06576), Johnson's [Perceptual Losses for Real-Time Style Transfer and Super-Resolution](http://cs.stanford.edu/people/jcjohns/eccv16/), and Ulyanov's [Instance Normalization](https://arxiv.org/abs/1607.08022). 

## Video Stylization 
Here we transformed every frame in a video, then combined the results. [Click to go to the full demo on YouTube!](https://www.youtube.com/watch?v=xVJwwWQlQ1o) The style here is Udnie, as above.
<div align = 'center'>
     <a href = 'https://www.youtube.com/watch?v=xVJwwWQlQ1o'>
        <img src = 'examples/results/fox_udnie.gif' alt = 'Stylized fox video. Click to go to YouTube!' width = '800px' height = '400px'>
     </a>
</div>

See how to generate these videos [here](stylizing-video)!

## Image Stylization
We added styles from various paintings to a photo of Chicago. Click on thumbnails to see full applied style images.
<div align='center'>
<img src = 'examples/content/chicago.jpg' height="200px">
</div>
     
<div align = 'center'>
<a href = 'examples/style/wave.jpg'><img src = 'examples/thumbs/wave.jpg' height = '200px'></a>
<img src = 'examples/results/chicago_wave.jpg' height = '200px'>
<img src = 'examples/results/chicago_udnie.jpg' height = '200px'>
<a href = 'examples/style/udnie.jpg'><img src = 'examples/thumbs/udnie.jpg' height = '200px'></a>
<br>
<a href = 'examples/style/rain_princess.jpg'><img src = 'examples/thumbs/rain_princess.jpg' height = '200px'></a>
<img src = 'examples/results/chicago_rain_princess.jpg' height = '200px'>
<img src = 'examples/results/chicago_la_muse.jpg' height = '200px'>
<a href = 'examples/style/la_muse.jpg'><img src = 'examples/thumbs/la_muse.jpg' height = '200px'></a>

<br>
<a href = 'examples/style/the_shipwreck_of_the_minotaur.jpg'><img src = 'examples/thumbs/the_shipwreck_of_the_minotaur.jpg' height = '200px'></a>
<img src = 'examples/results/chicago_wreck.jpg' height = '200px'>
<img src = 'examples/results/chicago_the_scream.jpg' height = '200px'>
<a href = 'examples/style/the_scream.jpg'><img src = 'examples/thumbs/the_scream.jpg' height = '200px'></a>
</div>

## Implementation Details
Our implementation uses TensorFlow to train a fast style transfer network. We use roughly the same transformation network as described in Johnson, except that batch normalization is replaced with Ulyanov's instance normalization, and the scaling/offset of the output `tanh` layer is slightly different. We use a loss function close to the one described in Gatys, using VGG19 instead of VGG16 and typically using "shallower" layers than in Johnson's implementation (e.g. we use `relu1_1` rather than `relu1_2`). Empirically, this results in larger scale style features in transformations.

## Documentation
### Training Style Transfer Networks
Use `style.py` to train a new style transfer network. Run `python style.py` to view all the possible parameters. Training takes 4-6 hours on a Maxwell Titan X. [More detailed documentation here](docs.md#style). **Before you run this, you should run `setup.sh`**. Example usage:

    python style.py --style examples/style/wave.jpg \
      --checkpoint-dir saver \
      --test examples/content/chicago.jpg \
      --test-dir test \
      --content-weight 1.5e1 \
      --checkpoint-iterations 1000 \
      --batch-size 20

### Evaluating Style Transfer Networks
Use `evaluate.py` to evaluate a style transfer network. Run `python evaluate.py` to view all the possible parameters. Evaluation takes 100 ms per frame (when batch size is 1) on a Maxwell Titan X. [More detailed documentation here](docs.md#evaluate). Takes several seconds per frame on a CPU. **Models for evaluation are [located here](https://www.dropbox.com/sh/exlysa7n1j397fp/AACl7lXX4mkTBZP14uX709Npa?dl=0)**. Example usage:

    python evaluate.py --style examples/style/wave.jpg \
      --checkpoint-dir saver \
      --in-path all_frames/ \
      --out-path transformed_frames/ \
      --device /gpu:2 \
      --batch-size 20

### Stylizing Video
Use `transform_video.py` to transfer style into a video. Run `python transform_video.py` to view all the possible parameters. Requires `ffmpeg`. [More detailed documentation here](docs.md#video). Example usage:

    python transform_video.py --in_path shibe.mp4 \
      --checkpoint-dir saver \
      --out-path shibe_la_muse.mp4 \
      --device /gpu:0 \
      --batch-size 20

### Requirements
You will need the following to run the above:
- TensorFlow .11
- Python 2, Pillow, scipy, numpy
- If you want to train (and don't want to wait for 4 months):
  - A decent GPU
  - All the required NVIDIA software to run TF on a GPU (cuda, etc)
- ffmpeg if you want to stylize video

### Attributions/Thanks
- This project could not have happened without the advice (and GPU access) given by [Anish Athalye](http://www.anishathalye.com/). 
  - The project also borrowed some code from Anish's [Neural Style](https://github.com/anishathalye/neural-style/)
- Some readme/docs formatting was borrowed from Justin Johnson's [Fast Neural Style](https://github.com/jcjohnson/fast-neural-style)
- The image of the Stata Center at the very beginning of the README was taken by [Juan Paulo](https://juanpaulo.me/)

### License
Copyright (c) 2016 Logan Engstrom. Contact me for commercial use (email: engstrom at my university's domain dot edu). Free for research/noncommercial use, as long as proper attribution is given and this copyright notice is retained.

