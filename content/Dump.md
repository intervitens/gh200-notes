

## LLaMAfac

## VLLM

Run on head node
```
ray start --block --head --port=6379
```
Run on other nodes
```
ray start --block  --address=192.168.136.183:6379
```
Install vllm to venv in /data


export PATH=$PATH:/root/.local/bin
cd /data/LLaMA-Factory
export RANK=