### Adding a dataset
add to `data/dataset_info.json`
- Sharegpt dataset example
```
  "kalo_opus": {
    "hf_hub_url": "kalomaze/Opus_Instruct_25k",
    "formatting": "sharegpt"
  },
```

### Example configs
```yaml
### model
model_name_or_path: ./Qwen_Qwen2.5-32B

### method
stage: sft
do_train: true
finetuning_type: full
deepspeed: ds_z3_config.json

### dataset
dataset: kalo_opus
template: chatml
cutoff_len: 16384
  #max_samples: 1000
overwrite_cache: true
preprocessing_num_workers: 16
neat_packing: true

### output
output_dir: saves/qwen2_5-32b_full_sft
logging_steps: 10
save_steps: 200
plot_loss: true
overwrite_output_dir: true

### train
optim: paged_adamw_8bit
per_device_train_batch_size: 1
gradient_accumulation_steps: 2
learning_rate: 1.0e-5
num_train_epochs: 3.0
lr_scheduler_type: cosine
warmup_ratio: 0.1
bf16: true
ddp_timeout: 180000000
enable_liger_kernel: true
flash_attn: fa2

### eval
#val_size: 0.1
#per_device_eval_batch_size: 1
#eval_strategy: steps
#eval_steps: 500
```


