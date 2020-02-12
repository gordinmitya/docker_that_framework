# Mobile DNN framework converters

Repo contains converter tools for mobile neural network inference frameworks wrapped into docker images.

Roadmap:
- [x] [Alibaba MNN](mnn/) (.onnx, .pb, .tflite to .mnn)
- [x] [TensorFlow](tf/) (.onnx to .tflite or .pb)
- [ ] MACE already have a [docker image](https://mace.readthedocs.io/en/latest/installation/using_docker.html). I just create a [mirror on DockerHub](https://hub.docker.com/r/gordinmitya/mace) to speed up downloading.
- [ ] [Tencent ncnn](https://github.com/Tencent/ncnn) (.onnx to .param and .bin)
