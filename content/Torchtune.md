## Torchtune

Run multinode, runs out of VRAM and hangs on checkpoint
```
tune run --nproc_per_node 1 --nnodes 4 --rdzv_id 69420 --rdzv_backend c10d --rdzv_endpoint 192.168.21.15:29500 full_finetune_distributed --config cfg.yaml
```
TODO: try out recent commit that potentially fixes the problem