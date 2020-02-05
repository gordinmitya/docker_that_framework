FROM tensorflow/tensorflow:latest-py3

RUN apt update && \
    apt install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip && \
    pip install --no-cache-dir onnx tensorflow-addons

RUN git clone https://github.com/onnx/onnx-tensorflow.git && \
    cd onnx-tensorflow && \
    pip install -e .