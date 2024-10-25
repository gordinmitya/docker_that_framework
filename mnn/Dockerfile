FROM gordinmitya/android-ndk AS builder

# https://vulkan.lunarg.com/sdk/home#linux
ENV VULKAN_SDK_VERSION=1.3.296

RUN wget -qO- https://packages.lunarg.com/lunarg-signing-key-pub.asc | tee /etc/apt/trusted.gpg.d/lunarg.asc && \
    wget -qO /etc/apt/sources.list.d/lunarg-vulkan-$VULKAN_SDK_VERSION-jammy.list https://packages.lunarg.com/vulkan/$VULKAN_SDK_VERSION/lunarg-vulkan-$VULKAN_SDK_VERSION-jammy.list

RUN apt update && apt install -y --no-install-recommends \
        # Vulkan SDK
        vulkan-sdk \
        # OpenCL
        ocl-icd-opencl-dev \
        # Protobuf
        libprotobuf-dev protobuf-compiler \
        # OpenGL
        libglew-dev libegl1-mesa-dev

ENV EXPORT_DIR=/export \
    MNN_ROOT=/mnn_framework
RUN mkdir -p $EXPORT_DIR $MNN_ROOT

WORKDIR $MNN_ROOT
RUN git clone https://github.com/alibaba/MNN.git $MNN_ROOT

RUN sh ./schema/generate.sh

RUN mkdir $MNN_ROOT/build_converter && \
    cd $MNN_ROOT/build_converter && \
    cmake .. \
    -DMNN_BUILD_CONVERTER=ON && \
    make -j$(nproc) && \
    cp ./MNNConvert $EXPORT_DIR

RUN for abi in "armeabi-v7a" "arm64-v8a" "x86_64"; do \
        mkdir $MNN_ROOT/"build_$abi" && \
        mkdir $EXPORT_DIR/lib_$abi && \
        cd $MNN_ROOT/"build_$abi" && \
        cmake .. \
        -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DANDROID_ABI=$abi \
        -DANDROID_STL=c++_static \
        -DANDROID_NATIVE_API_LEVEL=android-21  \
        -DANDROID_TOOLCHAIN=clang \
        -DMNN_VULKAN:BOOL=ON \
        -DMNN_OPENCL:BOOL=ON \
        -DMNN_OPENMP:BOOL=ON \
        -DMNN_OPENGL:BOOL=ON \
        -DMNN_DEBUG:BOOL=OFF \
        -DMNN_BUILD_BENCHMARK:BOOL=ON \
        -DMNN_BUILD_FOR_ANDROID_COMMAND=true \
        -DNATIVE_LIBRARY_OUTPUT=. && \
        make -j$(nproc) benchmark.out timeProfile.out && \
        find . -name "*.so" | while read solib; do cp $solib $EXPORT_DIR/lib_$abi; done && \
        cp ./benchmark.out $EXPORT_DIR && \
        cp ./timeProfile.out $EXPORT_DIR && \
        cd $MNN_ROOT ;\
    done

# WORKDIR /export

## TODO use multistage to reduce size
## how to grab all MNNConverter dependencies?
# FROM debian:stretch-slim
# WORKDIR /export
# COPY --from=builder /export/ /app/