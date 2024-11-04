Multinode doesn't work when installed in a venv

### Installation
Clone repository to shared storage
```
cd /data
export PATH=$PATH:/root/.local/bin
git clone https://github.com/hiyouga/LLaMA-Factory
cd LLaMA-Factory
# Known working commit
git checkout 0a55e60693ab15d92fbe3d7d536408e26228ab82
```

Install on all nodes
```
cd /data/LLaMA-Factory
export PATH=$PATH:/root/.local/bin
pip install --user -e ".[torch,metrics,deepspeed,liger-kernel,bitsandbytes]" --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
pip install --user --upgrade flash_attn --no-build-isolation --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
```

On each node, set it's rank from 0 to NNODES
```
export RANK=
```

Add `/root/.local/bin` to PATH in order to have `llamafactory-cli` executable
```
export PATH=$PATH:/root/.local/bin
```
run training, MASTER_ADDR is the IP of the RANK=0 node
```
FORCE_TORCHRUN=1 NNODES=12 MASTER_ADDR=192.168.136.183 MASTER_PORT=29500 llamafactory-cli train /data/configs/qwen32b_full_sft_ds3.yaml
```

### Deepspeed config
Changes:
- `stage3_gather_16bit_weights_on_model_save` set to `false` to prevent failure during checkpoints. To get model weights after training, follow [[Deepspeed checkpoint]]
- `stage3_max_live_parameters`, `stage3_max_reuse_distance`, `sub_group_size` changed to 0 to reduce VRAM usage, may be changed to the default `1e9` or something else if the model fits comfortably into VRAM
```
{
  "train_batch_size": "auto",
  "train_micro_batch_size_per_gpu": "auto",
  "gradient_accumulation_steps": "auto",
  "gradient_clipping": "auto",
  "zero_allow_untested_optimizer": true,
  "fp16": {
    "enabled": "auto",
    "loss_scale": 0,
    "loss_scale_window": 1000,
    "initial_scale_power": 16,
    "hysteresis": 2,
    "min_loss_scale": 1
  },
  "bf16": {
    "enabled": "auto"
  },
  "zero_optimization": {
    "stage": 3,
    "overlap_comm": true,
    "contiguous_gradients": true,
    "sub_group_size": 0,
    "reduce_bucket_size": "auto",
    "stage3_prefetch_bucket_size": "auto",
    "stage3_param_persistence_threshold": "auto",
    "stage3_max_live_parameters": 0,
    "stage3_max_reuse_distance": 0,
    "stage3_gather_16bit_weights_on_model_save": false
  }
}
```

### [[LLaMA-Factory examples]]