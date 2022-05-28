# [TNN by Tencent](https://github.com/Tencent/TNN/)

This image is available on [DockerHub](https://hub.docker.com/repository/docker/gordinmitya/tnn-convert).

There are two ways to tags:
* *latest* - onnx only, lightweight image;
* *tensorflow* - heawier by 2gb image with tensorflow.

**Converter**

```bash 
docker run --volume=$(pwd):/workspace --rm -it tnn-convert:latest python3 ./converter.py onnx2tnn /workspace/faceboxesv2.onnx -optimize -v=v1.0 -o /workspace/tnn/fp32
```

Show help:
```bash
docker run --rm -it tnn-convert:latest python3 ./converter.py onnx2tnn --help
```
