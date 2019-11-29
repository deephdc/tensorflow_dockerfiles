# Custom TensorFlow Dockerfiles

[![Build Status](https://jenkins.indigo-datacloud.eu/buildStatus/icon?job=Pipeline-as-code/DEEP-OC-org/tensorflow_dockerfiles/master)](https://jenkins.indigo-datacloud.eu/job/Pipeline-as-code/job/DEEP-OC-org/job/tensorflow_dockerfiles/job/master)

This directory houses Dockerfiles to build customized TensorFlow's Docker images to be deployed at 
[deephdc Docker Hub](https://hub.docker.com/r/deephdc/tensorflow).

The files are based on the original [Tensorflow Dockerfiles](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/dockerfiles).
The compatibility of CUDA/CUDNN versions for the GPU images can be found [here](https://www.tensorflow.org/install/source#tested_build_configurations).

We build cpu/gpu images for Ubuntu 18.04, Python 3.6 and Tensorflow versions 1.10.0/1.12.0, as original Tensorflow images use Ubuntu 16.04 and python 3.5. 

## Building

The Dockerfiles in the `dockerfiles` directory must have their build context set
to **the directory with this README.md** to copy in helper files. For example:

```bash
$ docker build -f ./dockerfiles/cpu.Dockerfile -t tensorflow .
```

This builds a tensorflow CPU-based image with the latest TensorFlow version, Ubuntu 18.04, and python3.
Each Dockerfile has its own set of available `--build-arg`s which are documented
in the Dockerfile itself.

**PLEASE NOTE, Dockerfiles are preliminary versions and may need further customization!**

## Running Locally Built Images

After building the image with the tag `tensorflow` (for example), 
use `docker run` to run the images.

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
$ docker run -u $(id -u):$(id -g) -v $(pwd):/my-devel -it tensorflow

# GPU-based images (set up [nvidia-docker2](https://github.com/NVIDIA/nvidia-docker) first)
$ docker run --runtime=nvidia -u $(id -u):$(id -g) -v $(pwd):/my-devel -it tensorflow

```

**N.B.** For either CPU-based or GPU-based images you can also use [udocker](https://github.com/indigo-dc/udocker).

**P.S.** These images do not come with the TensorFlow source code.

