# [TNN by Tencent](https://github.com/Tencent/TNN/)

This image is available on [DockerHub](https://hub.docker.com/repository/docker/gordinmitya/tnn-convert).

*Tensorflow* to use uncomment corresponding lines in `Dockerfile`.

**Converter**

```bash 
docker run --volume=$(pwd):/workspace --rm -it gordinmitya/tnn-convert:latest python3 ./converter.py onnx2tnn /workspace/model.onnx -optimize -v=v1.0 -o /workspace/tnn/fp32
```

Show help:
```bash
docker run --rm -it gordinmitya/tnn-convert:latest python3 ./converter.py onnx2tnn --help
```
