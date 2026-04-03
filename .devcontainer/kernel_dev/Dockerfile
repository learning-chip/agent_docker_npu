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
# (pin commit on 2026/02/14) https://github.com/tile-ai/tilelang-ascend/commit/96c01413021dedc7b3a6648a4efba9dc365bcca1
# includes the new PTO backend example: https://github.com/tile-ai/tilelang-ascend/pull/367
ARG TILELANG_ASCEND_COMMIT=96c01413021dedc7b3a6648a4efba9dc365bcca1
RUN git clone --recursive https://github.com/tile-ai/tilelang-ascend.git
RUN cd /sources/tilelang-ascend && git checkout $TILELANG_ASCEND_COMMIT && bash install_ascend.sh
RUN echo "source /sources/tilelang-ascend/set_env.sh" >> ~/.bashrc
#  ==== end of tilelang-ascend install ====


# ==== pto-dsl dependencies ====
# mirros https://github.com/huawei-csl/pto-dsl/blob/main/docker/Dockerfile

# certain operations need latest isa header, not CANN 8.5.0 default
# (pin commit on 2026/03/16)
ARG PTOISA_COMMIT=313817be696792a4e16a7ea5994ec98e34391613
WORKDIR /sources
RUN git clone https://gitcode.com/cann/pto-isa.git \
    && cd pto-isa && git checkout $PTOISA_COMMIT

# ARG ARCH=x86_64
ARG ARCH=aarch64
ARG RELEASE_REPO=zhangstevenunity/PTOAS
ARG RELEASE_VER=0.9
ARG RELEASE_TAG=v${RELEASE_VER}
ARG WHEEL_NAME=ptoas-${RELEASE_VER}-cp311-none-manylinux_2_34_${ARCH}.whl
ARG CLI_TAR_NAME=ptoas-bin-${ARCH}.tar.gz

WORKDIR /installers/
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
# ==== end of pto-dsl dependencies =====

WORKDIR /sources
