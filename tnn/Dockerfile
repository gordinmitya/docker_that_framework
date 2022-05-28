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

# RUN pip3 install tensorflow==1.15.0 tf2onnx

RUN git clone https://github.com/Tencent/TNN.git /opt/TNN

RUN cd /opt/TNN/tools/convert2tnn && \
    sed -i 's/make -j4/make -j`nproc`/g' ./build.sh && \
    bash ./build.sh && \
    rm -rf /opt/TNN/tools/convert2tnn/build && \
    python3 /opt/TNN/tools/convert2tnn/converter.py onnx2tnn -h

WORKDIR /opt/TNN/tools/convert2tnn/
