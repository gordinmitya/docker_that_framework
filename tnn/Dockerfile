FROM ubuntu:18.04

RUN apt-get update -y && \
    apt-get -y install --no-install-recommends \
    apt-utils \
    git cmake make \
    gcc g++ \
    protobuf-compiler libprotobuf-dev \
    python3 python3-dev python3-pip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN python3 -m pip install --upgrade pip setuptools && \
    pip3 install onnx onnxruntime numpy onnx-simplifier protobuf

RUN git clone https://github.com/Tencent/TNN.git /opt/TNN && \
    cd /opt/TNN/tools/convert2tnn && \
    sed 's/make -j4/make -j`nproc`/g' < build.sh > build.sh && \
    bash ./build.sh && \
    python3 /opt/TNN/tools/convert2tnn/converter.py -h

WORKDIR /opt/TNN/tools/convert2tnn/
