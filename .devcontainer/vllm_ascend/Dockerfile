FROM quay.io/ascend/vllm-ascend:v0.18.0rc1

WORKDIR /sources

#  ==== tilelang-ascend install ====
# (pin commit on 2026/04/03) https://github.com/tile-ai/tilelang-ascend/commit/3405ad8c03aa0d5de9423db6634a9a37725604b0
ARG TILELANG_ASCEND_COMMIT=3405ad8c03aa0d5de9423db6634a9a37725604b0
RUN git clone --recursive https://github.com/tile-ai/tilelang-ascend.git
WORKDIR /sources/tilelang-ascend
RUN git checkout $TILELANG_ASCEND_COMMIT
# RUN bash install_ascend.sh
# TODO: cmake error, to debug
# RUN echo "source /sources/tilelang-ascend/set_env.sh" >> ~/.bashrc
#  ==== end of tilelang-ascend install ====

# ==== pto dependencies ====

# certain operations need latest isa header, not CANN 8.5.0 default
# (pin commit on 2026/04/03) https://gitcode.com/cann/pto-isa/commit/6b97e220982e0743e00e815c4296b9a71a3f2162?ref=master
ARG PTOISA_COMMIT=6b97e220982e0743e00e815c4296b9a71a3f2162
WORKDIR /sources
RUN git clone https://gitcode.com/cann/pto-isa.git \
    && cd pto-isa && git checkout $PTOISA_COMMIT
#  ==== end of pto dependencies =====

WORKDIR /sources
