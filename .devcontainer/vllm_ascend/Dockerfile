FROM quay.io/ascend/vllm-ascend:v0.19.1rc1

# NOTE: vllm-ascend image sets OMP_NUM_THREADS to 1, leading to very slow build
# here use a better default
ENV OMP_NUM_THREADS=16

# ==== install cursor CLI ====
RUN apt-get update && apt-get install -y curl \
    && curl -fsSL https://cursor.com/install | bash \
    && ln -sf /root/.local/bin/agent /usr/local/bin/agent
# ==== end cursor CLI install ====
