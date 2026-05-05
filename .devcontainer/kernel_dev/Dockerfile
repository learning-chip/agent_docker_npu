FROM quay.io/ascend/cann:8.5.0-910b-ubuntu22.04-py3.11

RUN apt-get update && apt-get install -y \
    wget curl git vim \
    nodejs npm
# NOTE: nodejs and npm are only needed for claude code CLI

# for on-device execution
RUN pip install --no-cache-dir torch==2.9.0 --index-url https://download.pytorch.org/whl/cpu \
    && pip install --no-cache-dir torch-npu==2.9.0 \
    && pip install --no-cache-dir numpy pyyaml

# extra util
RUN pip install --no-cache-dir \
    pytest pybind11 nanobind setuptools wheel \
    ipython jupyterlab matplotlib pandas

RUN npm install -g @anthropic-ai/claude-code
# NOTE: only for claude-code CLI, no need to install this if using remote vscode extensions

# change this ununsed arg if need to force rebuild later lines
ARG CACHE_BURST=1

WORKDIR /sources

#  ==== tilelang-ascend install ====
# (pin commit on 2026/04/24) https://github.com/tile-ai/tilelang-ascend/commit/fc153041ae862b297e744f7a80d242d8041b07e6
# Includes DS-V4 kernel https://github.com/tile-ai/tilelang-ascend/pull/917
ARG TILELANG_ASCEND_COMMIT=fc153041ae862b297e744f7a80d242d8041b07e6
RUN git clone --recursive https://github.com/tile-ai/tilelang-ascend.git
WORKDIR /sources/tilelang-ascend

RUN git checkout $TILELANG_ASCEND_COMMIT && bash install_ascend.sh
RUN echo "source /sources/tilelang-ascend/set_env.sh" >> ~/.bashrc
#  ==== end of tilelang-ascend install ====

# ==== install cursor CLI ====
RUN apt-get update && apt-get install -y curl \
    && curl -fsSL https://cursor.com/install | bash \
    && ln -sf /root/.local/bin/agent /usr/local/bin/agent
# ==== end cursor CLI install ====

# ==== pto dependencies ====

# For updated FA style 
# https://gitcode.com/cann/pto-isa/commit/4e27a104f948e883e0bef44670252381bff794c5?ref=master
ARG PTOISA_COMMIT=4e27a104f948e883e0bef44670252381bff794c5
WORKDIR /sources
RUN git clone https://gitcode.com/cann/pto-isa \
    && cd pto-isa && git checkout $PTOISA_COMMIT

ENV PTO_LIB_PATH=/sources/pto-isa

# cache above layers unrelated to ptoas version change

# change this ununsed arg if need to force rebuild later lines
ARG CACHE_BURST=1

# ARG ARCH=x86_64
ARG ARCH=aarch64

# https://github.com/hw-native-sys/PTOAS/releases/tag/v0.35
# include the split pipes https://github.com/hw-native-sys/PTOAS/pull/606
ARG RELEASE_REPO=hw-native-sys/PTOAS
ARG RELEASE_VER=0.36
ARG RELEASE_TAG=v${RELEASE_VER}
ARG WHEEL_NAME=ptoas-${RELEASE_VER}-cp311-none-manylinux_2_34_${ARCH}.whl
ARG CLI_TAR_NAME=ptoas-bin-${ARCH}.tar.gz

WORKDIR /installers/

# download assets from https://github.com/huawei-csl/PTOAS/releases
RUN wget https://github.com/${RELEASE_REPO}/releases/download/${RELEASE_TAG}/${WHEEL_NAME}
RUN pip install ./${WHEEL_NAME}
RUN python -c "import mlir.ir; from mlir.dialects import pto"

RUN wget https://github.com/${RELEASE_REPO}/releases/download/${RELEASE_TAG}/${CLI_TAR_NAME}

# set up correct lib path for `ptoas` cli
ARG CLI_DIR=/installers/ptoas-cli
RUN mkdir $CLI_DIR && tar zxf ./${CLI_TAR_NAME} -C $CLI_DIR
RUN chmod +x $CLI_DIR/bin/ptoas
ENV PATH=$CLI_DIR/bin:$PATH
ENV LD_LIBRARY_PATH=$CLI_DIR/lib:$LD_LIBRARY_PATH

# test python binding + cli usage
WORKDIR /tests/
RUN wget https://raw.githubusercontent.com/${RELEASE_REPO}/refs/tags/${RELEASE_TAG}/test/samples/Abs/abs.py
RUN wget https://raw.githubusercontent.com/${RELEASE_REPO}/refs/tags/${RELEASE_TAG}/test/samples/MatMul/tmatmulk.py

RUN python ./tmatmulk.py > ./tmatmulk.pto
RUN ptoas ./tmatmulk.pto -o ./tmatmulk.cpp

RUN python ./abs.py > ./abs.pto
RUN ptoas --enable-insert-sync ./abs.pto -o ./abs.cpp

WORKDIR /sources
