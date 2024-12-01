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

ENV MNN_ROOT=/mnn_framework
RUN mkdir -p $MNN_ROOT

WORKDIR $MNN_ROOT

ARG MNN_VERSION="2.9.6"
RUN git clone --branch $MNN_VERSION --single-branch https://github.com/alibaba/MNN.git $MNN_ROOT

RUN sh ./schema/generate.sh

RUN mkdir $MNN_ROOT/build_converter && \
    cd $MNN_ROOT/build_converter && \
    cmake .. \
    -DMNN_BUILD_CONVERTER=ON \
    -DCMAKE_BUILD_TYPE=Release \
    -DMNN_BUILD_SHARED_LIBS=OFF && \
    make -j$(nproc) MNNConvert

# add other targets space separated "armeabi-v7a" "arm64-v8a" "x86_64"
RUN for abi in "arm64-v8a"; do \
        mkdir $MNN_ROOT/"build_$abi" && \
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
        -DMNN_OPENGL:BOOL=ON \
        -DMNN_DEBUG:BOOL=OFF \
        -DMNN_USE_LOGCAT:BOOL=OFF \
        -DMNN_BUILD_BENCHMARK:BOOL=ON \
        -DMNN_BUILD_TOOLS:BOOL=ON \
        -DMNN_BUILD_FOR_ANDROID_COMMAND=true \
        -DNATIVE_LIBRARY_OUTPUT=. && \
        make -j$(nproc) && \
        cd $MNN_ROOT ;\
    done

RUN apt install -y --no-install-recommends \
    python3 \
    python3-pip

RUN --mount=type=cache,target=/root/.cache/pip \
    pip3 install --upgrade pip && \
    pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

RUN --mount=type=cache,target=/root/.cache/pip \
    pip3 install timm \
    onnx onnxsim onnxruntime \
    jupyterlab \
    tqdm ipywidgets \
    pandas matplotlib