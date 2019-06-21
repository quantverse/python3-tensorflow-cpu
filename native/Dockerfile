# quantverse/python3-tensorflow-cpu:v1.9-native
FROM ubuntu:bionic

# install packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends software-properties-common curl python3-dev python3-wheel python3-setuptools python3-pip git build-essential pkg-config zip zlib1g-dev unzip openjdk-8-jdk \
    && ln -s /usr/bin/python3 /usr/bin/python \
    && rm -rf /var/lib/apt/lists/*

# install bazel
RUN curl -L https://github.com/bazelbuild/bazel/releases/download/0.13.0/bazel-0.13.0-without-jdk-installer-linux-x86_64.sh --output bazel.sh \
    && chmod +x bazel.sh \
    && ./bazel.sh

# install few pip modules
RUN pip3 install numpy

# install tensorflow
RUN git clone https://github.com/tensorflow/tensorflow /tf \
    && cd /tf \
    && git checkout r1.9 \
    && export PYTHON_BIN_PATH=/usr/bin/python \
    && export CC_OPT_FLAGS=-march=native \
    && export TF_NEED_JEMALLOC=1 \
    && export TF_NEED_GCP=0 \
    && export TF_NEED_HDFS=0 \
    && export TF_ENABLE_XLA=0 \
    && export USE_DEFAULT_PYTHON_LIB_PATH=1 \
    && export TF_NEED_OPENCL=0 \
    && export TF_NEED_CUDA=0 \
    && ./configure \
    && bazel build --config=opt --genrule_strategy=standalone --spawn_strategy=standalone //tensorflow/tools/pip_package:build_pip_package \
    && bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg \
    && pip3 install /tmp/tensorflow_pkg/* \
    && cd / && rm -rf tf

CMD ["python"]
