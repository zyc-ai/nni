# Copyright (c) Microsoft Corporation.
# Licensed under the MIT license.

FROM nvidia/cuda:9.2-cudnn7-runtime-ubuntu16.04

LABEL maintainer='Microsoft NNI Team<nni@microsoft.com>'

RUN DEBIAN_FRONTEND=noninteractive && \
    apt-get -y update && \
    apt-get -y install sudo \
    apt-utils \
    git \
    curl \
    vim \
    unzip \
    wget \
    build-essential \
    cmake \
    libopenblas-dev \
    automake \
    openssh-client \
    openssh-server \
    lsof \
    python3.5 \
    python3-dev \
    python3-pip \
    python3-tk \
    libcupti-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#
# update pip
#
RUN python3 -m pip install --upgrade pip setuptools==41.6.0

# numpy scipy
RUN python3 -m pip --no-cache-dir install numpy==1.17.4 scipy==1.3.1

#
# Tensorflow
#
RUN python3 -m pip --no-cache-dir install tensorflow-gpu==2.0.0

#
# Keras
#
RUN python3 -m pip --no-cache-dir install Keras==2.3.1

#
# PyTorch
#
RUN python3 -m pip --no-cache-dir install torch==1.3.1
RUN python3 -m pip install torchvision==0.4.2

#
# sklearn
#
RUN python3 -m pip --no-cache-dir install scikit-learn==0.21.3

#
# pandas lightgbm
#
RUN python3 -m pip --no-cache-dir install pandas==0.25.3 lightgbm==2.3.0

#
# Install NNI
#
RUN python3 -m pip --no-cache-dir install nni

ENV PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/root/.local/bin:/usr/bin:/bin:/sbin

WORKDIR /root
