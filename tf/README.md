# Tensorflow by Google

This image is available on [DockerHub](https://hub.docker.com/repository/docker/gordinmitya/onnx2tflite/).

## TensorFlow conversion
```bash
onnx-tf convert -i /data/mobilenet_v2.onnx -o /data/model.pb
```

## TFLite conversion

You have to convert .onnx to .pb first.

`--input_arrays` and `--output_arrays` you can see for instance in [Neutron](https://github.com/lutzroeder/netron) app.

```bash
tflite_convert --enable_v1_converter \
    --graph_def_file /data/model.pb \
    --output_file /data/model.tflite \
    --input_arrays input.1 \
    --output_arrays 465
```