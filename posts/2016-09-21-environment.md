---
title: Setting up Deep Learning Environment using Docker
tags: Docker
---

Setting up a deep learning development environment sometime can be a bit painful. There was a certain point in time, [Theano](http://deeplearning.net/software/theano/) and [Tensorflow](https://www.tensorflow.org/) recommend different version of [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit) and [cuDNN](https://developer.nvidia.com/cudnn). To install different versions from official recommendation, it requires developer to install the library from source code. The most painful time is that those source code requires different version of [GNU Compiler Collection (GCC)](https://gcc.gnu.org/) than the current GCC version on my computer. The whole process is rather painful and unfruitful.

Luckily, [Docker](https://www.docker.com/) comes to rescue! We just need to install different versions of software library in different image and everything should work well (at the time this post is written, the official Theano and Tensorflow can run on the CUDA Toolkit 7.5 and cuDNN 5.0). The docker files can be found in [the repository](https://github.com/senbong87/deep-learning-dev-environment).

After downloading the Git repository, we just have to build the two images using following command in the project root:

```
docker build -t senbong/lasagne:base -f lasagne/Dockerfile lasagne
docker build -t senbong/tensorflow:base -f tensorflow/Dockerfile tensorflow
```

After the two images have been built, we just need to create a container and run it:

```
nvidia-docker run --rm -it --volume `pwd`/Workspace:/root/Workspace senbong/lasagne:base /bin/bash

# or

nvidia-docker run --rm -it --volume `pwd`/Workspace:/root/Workspace senbong/tensorflow:base /bin/bash
```

Here, the `--rm` flag suggests that the container will be remove after the command exits whereas `-it` flags (`-i` and `-t` flags) instruct Docker to keep the STDIN open and create a virtual terminal. The `--volume` flag bind the file/directory in the host computer to the container's file/directory. We can put our source code inside the working directory and run the code inside the container. With this new tool, the software package management is much easier without worrying about breaking the dependencies of the host computer.
