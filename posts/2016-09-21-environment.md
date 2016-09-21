---
title: Setting up Deep Learning Environment using Docker
tags: Docker
---

Setting up a deep learning development environment sometime can be a bit painful. There was a certain point in time, [Theano](http://deeplearning.net/software/theano/) and [Tensorflow](https://www.tensorflow.org/) recommend different version of [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit) and [cuDNN](https://developer.nvidia.com/cudnn). To install different versions from official recommendation, it requires developer to install the library from source code. The most painful time is that those source code requires different version of [GNU Compiler Collection (GCC)](https://gcc.gnu.org/) than the current GCC version on my computer. The whole process is rather painful and unfruitful.

Luckily, [Docker](https://www.docker.com/) comes to rescue! We just need to install different versions of software library in different image and everything should work well. The docker files can be found in the [repository](https://github.com/senbong87/deep-learning-dev-environment).

### Building Docker Images

After downloading the Git repository, we just have to build the two images using following command in the project root:

```bash
docker build -t senbong/lasagne:base -f lasagne/Dockerfile lasagne
docker build -t senbong/tensorflow:base -f tensorflow/Dockerfile tensorflow
```

After the two images have been built, we just need to create a container and run it:

```bash
nvidia-docker run --rm -it --volume `pwd`/Workspace:/root/Workspace senbong/lasagne:base /bin/bash

# and/or

nvidia-docker run --rm -it --volume `pwd`/Workspace:/root/Workspace senbong/tensorflow:base /bin/bash
```

Here, the `--rm` flag suggests that the container will be remove after the command exits whereas `-it` flags (`-i` and `-t` flags) instruct Docker to keep the STDIN open and create a virtual terminal. The `--volume` flag bind the file/directory in the host computer to the container's file/directory. We can put our source code inside the working directory and run the code inside the container. With this new tool, the software package management is much easier without worrying about breaking the dependencies of the host computer.

### Test Run

Let's use one of the docker images to run an interesting application. [Neural Style](https://arxiv.org/abs/1508.06576) is an application to use the convolutionary neural network to combine the content and style two images into a single image. There is an implementation of the algorithm using Tensorflow and it can be downloaded in the GitHub [repository](https://github.com/anishathalye/neural-style).

```bash
# download the neural style source code
git clone https://github.com/anishathalye/neural-style

# create a container to run the code
nvidia-docker run --rm -it -w /root/Workspace \
    --volume `pwd`/neural-style:/root/Workspace senbong/tensorflow:base /bin/bash
```

After accessing into the container's shell, we should be in the project root of the neural-style. To run the example, we have to download the pre-trained VGG network. In this post, I will combine my graduation photo with the famous [Mona Lisa](https://en.wikipedia.org/wiki/Mona_Lisa) painting.

```bash
# download the pre-trained VGG network (this may take some time...)
wget http://www.vlfeat.org/matconvnet/models/beta16/imagenet-vgg-verydeep-19.mat

# generate the combined image
python neural_style.py --content photo.jpg --styles mona_lisa.jpg --output combined_photo.jpg
```

And this is the result...

![](/images/photo.jpg "my graduation photo") +
![](/images/mona_lisa.jpg "Mona Lisa painting"){#neural-style-mona-lisa} =
![](/images/combined_photo.jpg "combined photo"){#neural-style-combined}

Ok... I think I should use another photo... =P
