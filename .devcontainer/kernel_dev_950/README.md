Docker image for NPU kernel development

## Build images

Two LLVM variants are available from the same Dockerfile:

```bash
# LLVM 19 (vpto-dev/llvm-project:feature-vpto) — for PTOAS main branch
docker build --build-arg LLVM_REF=feature-vpto \
  -t agent_npu:cann_950_9.0.0_vpto_llvm19 .

# LLVM 21 (vpto-dev/llvm-project:feature-vpto-llvm21) — for PTOAS feature-vmi
docker build --build-arg LLVM_REF=feature-vpto-llvm21 \
  -t agent_npu:cann_950_9.0.0_vpto_llvm21 .
```

`devcontainer.host-ca.json` uses the LLVM 21 image by default.

## Environment variables

- `API_KEY_DASHSCOPE` — DashScope API key for OpenCode (`qwen3.7-max` via compatible-mode endpoint)
- `OPENAI_API_KEY` — API key for Codex (RightCode provider)

## Run container directly (optional)

```bash
HOST_MOUNT_DIR=$HOME/work_code/workdir_for_agent  # do not let agent access other files

docker run --rm -it --ipc=host --privileged \
    -e API_KEY_DASHSCOPE=$API_KEY_DASHSCOPE \
    --device=/dev/davinci0 --device=/dev/davinci1 \
    --device=/dev/davinci2 --device=/dev/davinci3 \
    --device=/dev/davinci4 --device=/dev/davinci5 \
    --device=/dev/davinci6 --device=/dev/davinci7 \
    --device=/dev/davinci_manager \
    --device=/dev/devmm_svm \
    --device=/dev/hisi_hdc \
    -v /usr/local/bin/npu-smi:/usr/local/bin/npu-smi:ro \
    -v /usr/local/Ascend/driver:/usr/local/Ascend/driver:ro \
    -v /etc/ascend_install.info:/etc/ascend_install.info:ro \
    -v $HOST_MOUNT_DIR:/workdir \
    -w /workdir \
    agent_npu:cann_950_9.0.0_vpto_llvm21 /bin/bash
```

Use `agent_npu:cann_950_9.0.0_vpto_llvm19` instead when working against PTOAS `main`.

```bash
# if just running host-side CA model, no need to mount device
docker run --rm -it \
    -e API_KEY_DASHSCOPE=$API_KEY_DASHSCOPE \
    -e OPENAI_API_KEY=$OPENAI_API_KEY \
    -v $HOME/work_code/workdir_for_agent:/workdir \
    -w /workdir \
    agent_npu:cann_950_9.0.0_vpto_llvm21 /bin/bash
```
