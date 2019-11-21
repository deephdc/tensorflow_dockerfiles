<div align="center">
<img src="https://marketplace.deep-hybrid-datacloud.eu/images/logo-deep.png" alt="logo" width="300"/>
</div>

# Custom TensorFlow Dockerfiles

[![Build Status](https://jenkins.indigo-datacloud.eu/buildStatus/icon?job=Pipeline-as-code/DEEP-OC-org/tensorflow_dockerfiles/master)](https://jenkins.indigo-datacloud.eu/job/Pipeline-as-code/job/DEEP-OC-org/job/tensorflow_dockerfiles/job/master)

This directory houses Dockerfiles to build customized TensorFlow's Docker images to be deployed at 
[deephdc Docker Hub](https://hub.docker.com/r/deephdc/tensorflow).

The files are based on the original [Tensorflow Dockerfiles](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/dockerfiles).
The compatibility of CUDA/CUDNN versions for the GPU images can be found [here](https://www.tensorflow.org/install/source#tested_build_configurations).

We currently build [cpu/gpu] images for Ubuntu 18.04, Python 3.6 and Tensorflow versions 1.[10.0/12.0/14.0].

## Building

The Dockerfiles in the `dockerfiles` directory must have their build context set
to **the directory with this README.md** to copy in helper files. For example:

```bash
$ docker build -f ./dockerfiles/cpu.Dockerfile -t tf .
```

Each Dockerfile has its own set of available `--build-arg`s which are documented
in the Dockerfile itself.

**PLEASE NOTE, Dockerfiles are preliminary versions and may need further customization!**

## Running Locally Built Images

After building the image with the tag `tf` (for example), use `docker run` to
run the images.

Note for new Docker users: the `-v` and `-u` flags share directories and
permissions between the Docker container and your machine. Without `-v`, your
work will be wiped once the container quits, and without `-u`, files created by
the container will have the wrong file permissions on your host machine. Check
out the
[Docker run documentation](https://docs.docker.com/engine/reference/run/) for
more info.

```bash
# Volume mount (-v) is optional but highly recommended, especially for Jupyter.
# User permissions (-u) are required if you use (-v).

# CPU-based images
$ docker run -u $(id -u):$(id -g) -v $(pwd):/my-devel -it tf

# GPU-based images (set up nvidia-docker2 first)
$ docker run --runtime=nvidia -u $(id -u):$(id -g) -v $(pwd):/my-devel -it tf

# Images with Jupyter run on port 8888 and need a volume for your notebooks
# You can change $(PWD) to the full path to a directory if your notebooks
# live outside the current directory.
$ docker run --user $(id -u):$(id -g) -p 8888:8888 -v $(PWD):/tf/notebooks -it tf
```

These images do not come with the TensorFlow source code -- but the development
images have git included, so you can `git clone` it yourself.

