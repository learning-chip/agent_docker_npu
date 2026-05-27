CANN 9.0.0.beta1 base image (custom build from installers on this server).

## Installers

Pre-downloaded `.run` packages live at `/scratch/ascend-downloads/cann_installers/`:

- `Ascend-cann-toolkit_9.0.0-beta.1_linux-${ARCH}.run`
- `Ascend-cann-nnal_9.0.0-beta.1_linux-${ARCH}.run`
- `Ascend-cann-950-ops_9.0.0_linux-${ARCH}.run` (formal ops; mixed with beta1 toolkit per [PTOAS#380](https://github.com/mouliangyu/PTOAS/issues/380))

To refresh installers manually:

```bash
ARCH=aarch64
CANN_TOOLKIT_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.T2/Ascend-cann-toolkit_9.0.0-beta.1_linux-${ARCH}.run
CANN_NNAL_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.T2/Ascend-cann-nnal_9.0.0-beta.1_linux-${ARCH}.run
CANN_OPS_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.0/Ascend-cann-950-ops_9.0.0_linux-${ARCH}.run

wget --header="Referer: https://www.hiascend.com/" "$CANN_TOOLKIT_URL"
wget --header="Referer: https://www.hiascend.com/" "$CANN_NNAL_URL"
wget --header="Referer: https://www.hiascend.com/" "$CANN_OPS_URL"

mv Ascend-cann-*.run /scratch/ascend-downloads/cann_installers/
```

## Build

Build context must be `/scratch/ascend-downloads` so `cann_installers/` is available to `COPY`.
The `-f` path is resolved from your **current working directory**, not the build context.

```bash
# Run from this directory (where Dockerfile lives), or cd here first:
#   cd agent_docker_npu/.devcontainer/cann_9.0.0.beta1
docker build \
  -f Dockerfile \
  -t ascend/cann:9.0.0.beta1-950-ubuntu22.04-py3.11 \
  /scratch/ascend-downloads
```

For aarch64 NPU hosts, the default platform is usually sufficient. For cross-builds, pass `--platform linux/arm64` (or `linux/amd64` once x86_64 installers are present in `cann_installers/`).

## Further add ptoas dependencies

```bash
docker build -f Dockerfile.ptoas_deps -t agent_npu_cann_950:9.0.0.beta1 .
```
