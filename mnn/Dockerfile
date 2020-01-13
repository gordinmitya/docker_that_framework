FROM gordinmitya/android-ndk

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

RUN git clone https://github.com/alibaba/MNN.git && \
    cd MNN && \
    ./schema/generate.sh

RUN mkdir MNN/build_converter && \
    cd MNN/build_converter && \
    cmake .. \
    -DMNN_BUILD_CONVERTER=true -DMNN_BUILD_SHARED_LIBS=false && \
    make -j8

RUN EXPORT_DIR=export/converter && BUILD_DIR=MNN/build_converter && \
    mkdir -p $EXPORT_DIR && \
    cp $BUILD_DIR/MNNConvert $EXPORT_DIR

RUN mkdir MNN/build_benchmark && \
    cd MNN/build_benchmark && \
    cmake .. \
    -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DANDROID_ABI="arm64-v8a" \
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
    make -j8 benchmark.out timeProfile.out
    
RUN EXPORT_DIR=export/benchmark && BUILD_DIR=MNN/build_benchmark \
    mkdir -p $EXPORT_DIR && \
    find $BUILD_DIR -name "*.so" | while read solib; do cp $solib $EXPORT_DIR; done && \
    cp $BUILD_DIR/benchmark.out $EXPORT_DIR && \
    cp $BUILD_DIR/timeProfile.out $EXPORT_DIR
