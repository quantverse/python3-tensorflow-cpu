# quantverse/python3-tensorflow-cpu:v2.0-native
# use this for tensorflow v2
FROM ubuntu:bionic

# install packages and bazel
RUN apt update \
    && apt install -y --no-install-recommends software-properties-common curl python3-dev python3-wheel python3-setuptools python3-pip git build-essential pkg-config zip zlib1g-dev unzip openjdk-8-jdk \
    && ln -s /usr/bin/python3 /usr/bin/python \
    && rm -rf /var/lib/apt/lists/*

# install bazel
RUN curl -L https://github.com/bazelbuild/bazel/releases/download/0.29.1/bazel-0.29.1-installer-linux-x86_64.sh --output bazel.sh \
    && chmod +x bazel.sh \
    && ./bazel.sh

# install few pip modules
RUN python -m pip install pip --upgrade \
    && pip install -U six numpy wheel setuptools mock 'future>=0.17.1' \
    && pip install -U keras_applications --no-deps \
    && pip install -U keras_preprocessing --no-deps

# install tensorflow
RUN git clone https://github.com/tensorflow/tensorflow /tf \
    && cd /tf \
    && git checkout r2.0 \
    && export PYTHON_BIN_PATH=/usr/bin/python \
    && export CC_OPT_FLAGS=-march=native \
    && export TF_ENABLE_XLA=0 \
    && export USE_DEFAULT_PYTHON_LIB_PATH=1 \
    && export TF_NEED_OPENCL_SYCL=0 \
    && export TF_NEED_ROCM=0 \
    && export TF_NEED_CUDA=0 \
    && export TF_DOWNLOAD_CLANG=0 \
    && export TF_SET_ANDROID_WORKSPACE=0 \
    && ./configure \
    && bazel build --config=opt --config=noaws --config=nogcp --config=nohdfs --config=nonccl --local_ram_resources="HOST_RAM*.5" //tensorflow/tools/pip_package:build_pip_package \
    && bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg \
    && pip install /tmp/tensorflow_pkg/* \
    && cd / && rm -rf tf

CMD ["python"]
