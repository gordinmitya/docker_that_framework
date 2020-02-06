# [MNN by Alibaba](https://github.com/alibaba/MNN)

This image is available on [DockerHub](https://hub.docker.com/repository/docker/gordinmitya/mnn/).

Docker image with compiled tools: 
* [MNNConvert](https://www.yuque.com/mnn/en/model_convert) – converter from TF,CAFFE,ONNX,TFLITE to MNN format;
* [timeProfile.out](https://www.yuque.com/mnn/en/tool_test) Operators' total time-consuming statistic;
* [benchmark.out](https://www.yuque.com/mnn/en/tool_benchmark) benchmark time for several models.

Also, it containers builded *.so* libraries for Android armeabi-v7a, arm64-v8a *(libMNN.so, libMNN_CL.so, libMNN_Express.so, libMNN_GL.so, libMNN_Vulkan.so)*.

## Usage

**Copy libraries**

```bash
docker run --rm -v /home/mitya/data:/data gordinmitya/mnn /bin/bash -c "cp -r lib_* /data"
```

**Converter**

```bash
docker run --rm -v /home/mitya/data:/data gordinmitya/mnn \
  /export/MNNConvert --framework ONNX \
    --modelFile /data/mobilenet_v2.onnx \
    --MNNModel /data/converted_model.mnn \
    --bizCode biz
```
Where:
* `--rm` remove container after execution,
* `-v /home/mitya/data:/data` – map local directory with model to /data directory inside container,
* `gordinmitya/mnn` image name from DockerHub,
* `/export/MNNConvert …` path to tool inside container and args.

Or just enter the container

```bash
docker run --rm -it -v /home/mitya/data:/data gordinmitya/mnn /bin/bash"
```

MNNConverter help:
```
Usage:
  MNNConvert [OPTION...]

  -h, --help            Convert Other Model Format To MNN Model

  -v, --version         show current version
  -f, --framework arg   model type, ex: [TF,CAFFE,ONNX,TFLITE,MNN]
      --modelFile arg   tensorflow Pb or caffeModel, ex: *.pb,*caffemodel
      --prototxt arg    only used for caffe, ex: *.prototxt
      --MNNModel arg    MNN model, ex: *.mnn
      --fp16            save Conv's weight/bias in half_float data type
      --benchmarkModel  Do NOT save big size data, such as Conv's weight,BN's
                        gamma,beta,mean and variance etc. Only used to test
                        the cost of the model
      --bizCode arg     MNN Model Flag, ex: MNN
      --debug           Enable debugging mode.
      --forTraining     whether or not to save training ops BN and Dropout,
                        default: false
```                        
