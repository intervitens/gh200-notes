Run on head node
```
ray start --block --head --port=6379
```
Run on other nodes
```
ray start --block  --address=192.168.136.183:6379
```
Install vllm to a venv in /data
```
python -m venv env_vllm
source env_vllm/bin/activate
pip install vllm --extra-index-url https://pypi.fluffyandfuzzy.cfd/simple/
```