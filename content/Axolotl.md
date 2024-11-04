"working" axo commit: 6e354682e3c1735d3f7fb9e362280c38e922260f

axo install 
```
cd /data
apt update
apt install python3.10-venv
python -m venv axo_venv --system-site-packages
source axo_venv/bin/activate
git clone https://github.com/axolotl-ai-cloud/axolotl
cd axolotl
git checkout 6e354682e3c1735d3f7fb9e362280c38e922260f
# Edit requirements.txt, delete autoawq, change xformers version to 0.0.28, bitsandbytes to 0.44.1
pip install -e . --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
pip install deepspeed==0.14.4 flash_attn==2.6.3 --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
pip install --upgrade pynvml

```

accelerate config for axo fsdp
```
(axo_venv) root@kalomaze-nvidia-pytorch-0:/data# cat ~/.cache/huggingface/accelerate/default_config.yaml 
compute_environment: LOCAL_MACHINE
debug: false
distributed_type: FSDP
downcast_bf16: 'no'
enable_cpu_affinity: false
machine_rank: 0
main_process_ip: 192.168.120.182
main_process_port: 1337
main_training_function: main
mixed_precision: 'no'
num_machines: 4
num_processes: 4
rdzv_backend: static
same_network: true
tpu_env: []
tpu_use_cluster: false
tpu_use_sudo: false
use_cpu: false
```
axo fsdp kinda working config (checkpoint broken, hangs)
```
fsdp:
  - full_shard
  - auto_wrap
fsdp_config:
  fsdp_limit_all_gathers: true
  fsdp_sync_module_states: true
  fsdp_offload_params: true
  fsdp_use_orig_params: false
  fsdp_cpu_ram_efficient_loading: true
  fsdp_auto_wrap_policy: TRANSFORMER_BASED_WRAP
  fsdp_transformer_layer_cls_to_wrap: LlamaDecoderLayer
  fsdp_state_dict_type: FULL_STATE_DICT
  fsdp_sharding_strategy: FULL_SHARD
  fsdp_backward_prefetch: BACKWARD_PRE
```
TODO: Figure out deepspeed, some instructions [here](https://github.com/axolotl-ai-cloud/axolotl/blob/640bb6c9f3c74cc0ad762f62045e2846bc7c065b/docs/multi-node.qmd)