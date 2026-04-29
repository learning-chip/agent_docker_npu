Docker image for vllm-ascend inference

## Build image

```bash
docker build -t vllm_ascend_dev:v0.18.0rc1 .
```

## Run container directly (optional)

```bash
docker run --rm -it --ipc=host --privileged \
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
    -v /scratch/model_weights:/scratch/model_weights \
    -v $HOME/work_code/workdir_for_agent:/workdir \
    -w /workdir \
    --name vllm_cursor_cli \
    vllm_ascend_dev:v0.18.0rc1 /bin/bash
```
