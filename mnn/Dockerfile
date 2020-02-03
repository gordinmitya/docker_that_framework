FROM gordinmitya/android-ndk AS builder

# Vulkan SDK 
# https://vulkan.lunarg.com/sdk/home#linux
RUN wget -qO - http://packages.lunarg.com/lunarg-signing-key-pub.asc | apt-key add - && \
    wget -qO /etc/apt/sources.list.d/lunarg-vulkan-1.1.126-bionic.list http://packages.lunarg.com/vulkan/1.1.126/lunarg-vulkan-1.1.126-bionic.list && \
    apt update && apt install -y --no-install-recommends vulkan-sdk && \
    # OpenCL
    apt install -y --no-install-recommends ocl-icd-opencl-dev && \
    # Protobuf
    apt install -y libprotobuf-dev protobuf-compiler && \
    # OpenGL
    apt install -y libglew-dev libegl1-mesa-dev

ENV EXPORT_DIR=/export BUILD_DIR=/build

RUN git clone https://github.com/alibaba/MNN.git $BUILD_DIR && \
    cd $BUILD_DIR && \
    ./schema/generate.sh && \
    mkdir -p $EXPORT_DIR

RUN mkdir $BUILD_DIR/build_converter && \
    cd $BUILD_DIR/build_converter && \
    cmake .. \
    -DMNN_BUILD_CONVERTER=true && \
    make -j8 && \
    cp ./MNNConvert $EXPORT_DIR
    
RUN for abi in "armeabi-v7a" "arm64-v8a"; do \
        mkdir $BUILD_DIR/"build_$abi" && \
        mkdir $EXPORT_DIR/lib_$abi && \
        cd $BUILD_DIR/"build_$abi" && \
        cmake .. \
        -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DANDROID_ABI=$abi \
        -DANDROID_STL=c++_static \
        -DCMAKE_BUILD_TYPE=Release \
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
        make -j8 benchmark.out timeProfile.out && \
        find . -name "*.so" | while read solib; do cp $solib $EXPORT_DIR/lib_$abi; done && \
        cp ./benchmark.out $EXPORT_DIR && \
        cp ./timeProfile.out $EXPORT_DIR && \
        cd $BUILD_DIR ;\
    done

WORKDIR /export

## TODO use multistage to reduce size
## how to grab all MNNConverter dependencies?
# FROM debian:stretch-slim
# WORKDIR /export
# COPY --from=builder /export/ /app/