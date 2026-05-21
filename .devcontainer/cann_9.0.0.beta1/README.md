Download CANN installer

```bash
ARCH=aarch64
CANN_TOOLKIT_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.T2/Ascend-cann-toolkit_9.0.0-beta.1_linux-${ARCH}.run
CANN_NNAL_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.T2/Ascend-cann-nnal_9.0.0-beta.1_linux-${ARCH}.run

# The beta1 op installer this doesn't exist for 950, I'll try mix beta1 CANN toolkit with formal ops package
# CANN_OPS_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.T2/Ascend-cann-950-ops_9.0.0-beta.1_linux-${ARCH}.run  # no file
CANN_OPS_URL=https://ascend-repo.obs.cn-east-2.myhuaweicloud.com/CANN/CANN%209.0.0/Ascend-cann-950-ops_9.0.0_linux-${ARCH}.run

wget $CANN_TOOLKIT_URL && wget $CANN_NNAL_URL && wget $CANN_OPS_URL

# put into /scratch/ascend-downloads/cann_installers/ on current server
```
