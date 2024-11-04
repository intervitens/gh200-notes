## NCCL debugging
building things with openmpi from ubuntu repos
```
sudo apt-get install openmpi-bin openmpi-common libopenmpi-dev
make MPI=1 MPI_HOME=/usr/lib/aarch64-linux-gnu/openmpi/
```
with built-in openmpi
```
make MPI=1 MPI_HOME=/usr/local/mpi/
```
run all-reduce test
```
mpirun --hostfile hosts.txt -np 2 ./build/all_reduce_perf -b 8 -e 256M -f 2 -g 1
```
hostfile example
```
10.69.20.1 slots=1
10.69.20.2 slots=1
```
You need to set up certificates to have passwordless ssh between all nodes
/etc/nccl.conf nccl debug config
```
NCCL_P2P_DISABLE=1
NCCL_IB_CUDA_SUPPORT=0
NCCL_IBEXT_DISABLE=1
NCCL_SOCKET_IFNAME=eth
NCCL_NET_GDR_LEVEL=5
NCCL_DEBUG_SUBSYS=ALL
NCCL_DEBUG=INFO
NCCL_IB_DISABLE=1
```
working config
```
NCCL_P2P_DISABLE=0
NCCL_IBEXT_DISABLE=0
NCCL_SOCKET_IFNAME=net
NCCL_NET_GDR_LEVEL=5
NCCL_IB_DISABLE=0
```