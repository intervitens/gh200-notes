tested axo commit: e4af51eb66ffea450bdcd55b10be2b52661ad17e
#### Install 
```
cd /data
apt update
apt install python3.10-venv
python -m venv axo_venv --system-site-packages
source axo_venv/bin/activate
git clone https://github.com/axolotl-ai-cloud/axolotl
cd axolotl
# Edit requirements.txt and setup.py, delete autoawq and torchao
pip install -e . --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
pip install deepspeed==0.15.3 flash_attn==2.6.3 --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
pip install --upgrade pynvml
```

#### DeepSpeed multinode
Needs to run under root
- Do [[Passwordless SSH setup]]
- On all nodes, install pdsh and fix `/usr/lib` folder permissions
```
apt update && apt install -y pdsh
chown root:root /usr/lib
```
- On all nodes, set up accelerate config
```
mkdir -p /root/.cache/huggingface/accelerate
vim /root/.cache/huggingface/accelerate/default_config.yaml
```
Change `num_machines` and `num_processes` depending on the number of nodes, set `machine_rank` to `[0-num_machines)` for each node, set `main_process_ip` to the IP of the `machine_rank: 0` node.
```yaml
compute_environment: LOCAL_MACHINE
debug: false
deepspeed_config:
  deepspeed_config_file: /data/axolotl/deepspeed_configs/zero3_bf16.json
  deepspeed_hostfile: /data/axolotl/hostfile
  deepspeed_multinode_launcher: pdsh
  zero3_init_flag: false
distributed_type: DEEPSPEED
downcast_bf16: 'no'
enable_cpu_affinity: false
machine_rank: 0
main_process_ip: 192.168.243.110
main_process_port: 1337
main_training_function: main
num_machines: 4
num_processes: 4
rdzv_backend: static
same_network: true
tpu_env: []
tpu_use_cluster: false
tpu_use_sudo: false
use_cpu: false
```
- Create a hostfile in `/data/axolotl/hostfile` that lists IP addresses of all nodes. Example for 4 nodes:
```
192.168.243.110 slots=1
192.168.89.74 slots=1
192.168.57.107 slots=1
192.168.21.18 slots=1
```
- Modify the deepspeed config file by setting `stage3_gather_16bit_weights_on_model_save: false` to prevent a hang during checkpointing
- Prepare dataset
```
python -m axolotl.cli.preprocess axo_config.yml
```
- Run training
```
accelerate launch -m axolotl.cli.train axo_config.yml
```
- Recover the final model weights from the DeepSpeed checkpoint: [[DeepSpeed checkpoint]]



#### FSDP
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