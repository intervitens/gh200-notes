To create virtual environments
```
apt install python3.10-venv
```
aarch64 pyorch installed from pip doesn't support CUDA, to make a venv add a `--system-site-packages` flag to use working systemwide pytorch
```
python -m venv env --system-site-packages
```

To enable pip cache, remove 
`global.no-cache-dir='true'` in
`/etc/xdg/pip/pip.conf` and `/etc/pip.conf`

To get nvcc when compiling some packages
```
export PATH=$PATH:/usr/local/cuda-12.6/bin
```

install cloudflared
```
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared jammy main' | sudo tee /etc/apt/sources.list.d/cloudflared.list
sudo apt-get update && sudo apt-get install cloudflared
```

hf_transfer significantly speeds up model uploads/downloads to huggingface
```
export HF_HUB_ENABLE_HF_TRANSFER=1
```