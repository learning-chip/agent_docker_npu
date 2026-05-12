FROM quay.io/ascend/cann:9.0.0-950-ubuntu22.04-py3.11

RUN apt-get update && apt-get install -y \
    wget curl git vim \
    ninja-build cmake ccache build-essential lld zip binutils patchelf chrpath \
    && rm -rf /var/lib/apt/lists/*

# for on-device execution
# according to https://gitcode.com/Ascend/pytorch, should use torch-npu==2.9.0.post2 for CANN 9.0.0
RUN pip install --no-cache-dir torch==2.9.0 --index-url https://download.pytorch.org/whl/cpu \
    && pip install --no-cache-dir torch-npu==2.9.0.post2 \
    && pip install --no-cache-dir numpy pyyaml
    
# extra util
RUN pip install --no-cache-dir \
    pytest pybind11 nanobind setuptools wheel \
    ipython jupyterlab matplotlib pandas

# cursor CLI
RUN apt-get update && apt-get install -y curl \
    && curl -fsSL https://cursor.com/install | bash \
    && ln -sf /root/.local/bin/agent /usr/local/bin/agent

# github cli https://github.com/cli/cli/blob/trunk/docs/install_linux.md
RUN apt update && apt install wget -y \
	&& mkdir -p -m 755 /etc/apt/keyrings \
	&& out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
	&& cat $out | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& mkdir -p -m 755 /etc/apt/sources.list.d \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" |  tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& apt update \
	&& apt install gh -y

# pre-built MLIR for ptoas & dsl development 
ENV WORKSPACE_DIR=/llvm-workspace
ENV LLVM_SOURCE_DIR=$WORKSPACE_DIR/llvm-project
ENV LLVM_BUILD_DIR=$LLVM_SOURCE_DIR/build-shared
ARG LLVM_TAG=llvmorg-19.1.7

WORKDIR $WORKSPACE_DIR
RUN git clone --depth 1 --branch ${LLVM_TAG} https://github.com/llvm/llvm-project.git

COPY cmake/LinuxHardeningCache.cmake /tmp/LinuxHardeningCache.cmake
WORKDIR $LLVM_SOURCE_DIR
RUN cmake -C /tmp/LinuxHardeningCache.cmake -G Ninja -S llvm -B $LLVM_BUILD_DIR \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DBUILD_SHARED_LIBS=ON \
    -DMLIR_ENABLE_BINDINGS_PYTHON=ON \
    -DPython3_EXECUTABLE="$(which python)" \
    -DCMAKE_BUILD_TYPE=Release \
    -DLLVM_TARGETS_TO_BUILD="host"

RUN ninja -C $LLVM_BUILD_DIR
