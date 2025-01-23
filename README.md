# ACAP Computer Vision SDK

##  Python 3.12

This branch was created to update Python to version 3.12, as well as to update the OpenCV and NumPy libraries to versions 4.8.0 and 1.26.4, respectively. The manifest file responsible for these changes is located at  `/armv7hf/Dockerfile.armv7hf`.

The Computer Vision SDK image packages are located under this `/axis` directory. The directory of a package, e.g., `/axis/opencv`, contain
the files needed for the applications as seen from the root of the application container. Thus, merging e.g., `/axis/opencv` with the root `/` of your
container will add the package correctly. This is what is done with the `COPY` commands in the example Dockerfile below.

Dependencies between packages currently need to be handled manually. That is, e.g., `python-numpy` does not include `python`. Rather, both
packages will have to be added to the application container to use the NumPy package.

## Instructions

1. Select a base image suitable for your camera platform, e.g., `arm32v7/ubuntu:20.04` for running Ubuntu 20.04 natively on the ARTPEC-7 platform.
2. Copy the packages needed for your application from the CV SDK, e.g., for an application running OpenCV in Python, the copied packages would include
OpenCV, Python, NumPy (OpenCV-Python dependency) and OpenBLAS (optimized math functions).

Thus, the Dockerfile for your application could be set up as:

```sh
FROM matheusdutra0207/acap-computer-vision-skd-ubuntu20.04:acap AS sdk
FROM --platform=linux/arm/v7 arm32v7/ubuntu:20.04

# # Add the CV packages
COPY --from=sdk /axis/python /
COPY --from=sdk /axis/numpy /
COPY --from=sdk /axis/openblas /
COPY --from=sdk /axis/opencv /

# Some applications written in Python 3
CMD ["sh", "-c", "python3 --version && python3 -c 'import numpy; print(f\"NumPy version: {numpy.__version__}\")' && python3 -c 'import cv2; print(f\"OpenCV version: {cv2.__version__}\")'"]
```

## Contents

* `/axis/opencv`: [OpenCV 4.8.0](https://github.com/opencv/opencv) with [VDO](https://www.axis.com/products/online-manual/s00004#t10157890)
  * A computer vision library with functionality that covers many different fields within computer vision.
The VDO integration allows accessing the camera's video streams through the OpenCV VideoCapture class. Compiled with OpenBLAS.
* `/axis/python`: Python
  * A Python 3.12 installation to allow easy prototyping and development of applications.
* Python packages
  * `/axis/python-numpy`: [NumPy](https://github.com/numpy/numpy) - Compiled with OpenBLAS.
* `/axis/openblas`: [OpenBLAS](https://github.com/xianyi/OpenBLAS)
  * A library with optimized linear algebra operations which can accelerate many applications.



