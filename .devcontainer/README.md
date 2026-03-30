## Build image

```bash
docker build -t agent_npu_cann:8.5.0 .
```

## Run container directly (optional)

```bash
HOST_MOUNT_DIR=$HOME/work_code/workdir_for_agent  # do not let agent access other files

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
    -v $HOST_MOUNT_DIR:/workdir \
    -w /workdir \
    -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
    agent_npu_cann:8.5.0 /bin/bash
```
