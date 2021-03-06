FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04
MAINTAINER caffe-maint@googlegroups.com

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python-dev \
        python-numpy \
        python-pip \
        python-scipy && \
    rm -rf /var/lib/apt/lists/*

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

# FIXME: clone a specific git tag and use ARG instead of ENV once DockerHub supports this.
ENV CLONE_TAG=master

RUN git clone -b ${CLONE_TAG} --depth 1 https://github.com/sfzhang15/RefineDet.git . && \
    cp Makefile.config.example Makefile.config && \
    make all -j"$(nproc)"

RUN sudo pip install --upgrade --ignore-installed six pip && \
    for req in $(cat python/requirements.txt) pydot easydict; do pip install $req; done && \
    make py -j"$(nproc)"
    
RUN pip3 install opencv-python
RUN pip3 install opencv-contrib-python

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
RUN echo "usr/lib/x86_64-linux-gnu" >> /etc/ld.so.conf.d/opencv.conf && ldconfig

#WORKDIR /workspace
