# quantverse/python3-tensorflow-cpu:v1-native
FROM ubuntu:xenial
LABEL maintainer "QUANTVERSE <info@quantverse.com>"

# install packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends software-properties-common curl python3-dev python3-wheel python3-setuptools python3-pip git build-essential \
    && ln -s /usr/bin/python3 /usr/bin/python \
    && rm -rf /var/lib/apt/lists/*

# install oracle java
RUN add-apt-repository ppa:webupd8team/java \
    && apt-get update \
    && echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections \
    && apt-get install -y oracle-java8-installer \
    && rm -rf /var/lib/apt/lists/*

# install bazel
RUN echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list \
    && curl https://bazel.build/bazel-release.pub.gpg | apt-key add - \
    && apt-get update \
    && apt-get install -y bazel \
    && rm -rf /var/lib/apt/lists/*

# install few pip modules
RUN pip3 install --upgrade pip \
    && pip3 install numpy

# install tensorflow
RUN git clone https://github.com/tensorflow/tensorflow /tf \
    && cd /tf \
    && git checkout r1.0 \
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
