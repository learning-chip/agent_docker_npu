FROM quay.io/ascend/vllm-ascend:v0.18.0rc1

WORKDIR /sources

# ==== install cursor CLI ====
RUN apt-get update && apt-get install -y curl \
    && curl -fsSL https://cursor.com/install | bash \
    && ln -sf /root/.local/bin/agent /usr/local/bin/agent
# ==== end cursor CLI install ====

#  ==== tilelang-ascend install ====
# (pin commit on 2026/04/03) https://github.com/tile-ai/tilelang-ascend/commit/3405ad8c03aa0d5de9423db6634a9a37725604b0
ARG TILELANG_ASCEND_COMMIT=3405ad8c03aa0d5de9423db6634a9a37725604b0
RUN git clone --recursive https://github.com/tile-ai/tilelang-ascend.git
WORKDIR /sources/tilelang-ascend
# temporary fix until https://github.com/tile-ai/tilelang-ascend/pull/771 merged
RUN git checkout $TILELANG_ASCEND_COMMIT \
    && sed -i '/echo "set(USE_ASCEND ON)" >> config.cmake/a echo '\''set(USE_GTEST OFF)'\'' >> config.cmake' install_ascend.sh \
    && unset OMP_NUM_THREADS \
    && bash install_ascend.sh
RUN echo "source /sources/tilelang-ascend/set_env.sh" >> ~/.bashrc
#  ==== end of tilelang-ascend install ====

# ==== pto dependencies ====

# certain operations need latest isa header, not CANN 8.5.0 default
# (pin commit on 2026/04/03) https://gitcode.com/cann/pto-isa/commit/6b97e220982e0743e00e815c4296b9a71a3f2162?ref=master
ARG PTOISA_COMMIT=6b97e220982e0743e00e815c4296b9a71a3f2162
WORKDIR /sources
RUN git clone https://gitcode.com/cann/pto-isa.git \
    && cd pto-isa && git checkout $PTOISA_COMMIT
ENV PTO_LIB_PATH=/sources/pto-isa/
#  ==== end of pto dependencies =====

# NOTE: vllm-ascend image sets OMP_NUM_THREADS to 1, leading to very slow build
# here use a better default
ENV OMP_NUM_THREADS=16

WORKDIR /sources
