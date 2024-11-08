Script for setting up passwordless SSH between all nodes [login_ssh.sh](files/login_ssh.sh)
```
python login_ssh.py --ips '192.168.136.183,192.168.107.33,192.168.120.191,192.168.99.22' --password 'example_pwd' --username test_user
```
TODO: Do this automatically when creating the cluster