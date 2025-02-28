# Multiple Nodes NCCL Test
Please make sure the same version of NCCL has been installed on all the nodes. On each node, the NCCL-Test is able to run well.

## How to run
1. Basic requirements are same as single node test, please refer the single node set up.
2. Please check the same version of OpenMPI has been installed on all the nodes. If not installed, please install it.
    ```bash
    apt install -y openmpi-bin openmpi-common libopenmpi-dev
    ```
3. Please check the passwordless SSH has been set up between all the nodes.
4. Rebuild the NCCL-Test on all the nodes. If the MPI_HOME is `/usr/lib/x86_64-linux-gnu/openmpi`
    ```bash
    cd nccl-tests
    make clean
    make MPI=1 MPI_HOME=/usr/lib/x86_64-linux-gnu/openmpi
    ```
5. Run the following command on the one node.
   Notes, the `btl_tcp_if_include` and `NCCL_SOCKET_IFNAME` should be the network interface name, would be bond0, eth0, etc. The `NCCL_IB_HCA` should be the HCA name, would be mlx5_0, mlx5_1, etc. The `ip` should be the IP address of the nodes. The `ip:8` means 8 slots on each node. In the example below, there will be 16 MPI processes running on 2 nodes, each process will use 1 GPU.
    ```bash
    mpirun --allow-run-as-root -np 16 --mca btl_tcp_if_include bond -H ip:8,ip:8 -x NCCL_SOCKET_IFNAME=bond -x NCCL_IB_HCA=mlx5_0,mlx5_3,mlx5_4,mlx5_5,mlx5_6,mlx5_9,mlx5_10,mlx5_11 ./build/all_reduce_perf -b 256M -e 8G -f 2 -g 1
    ```

    ```
    # nThread 1 nGpus 1 minBytes 268435456 maxBytes 8589934592 step: 2(factor) warmup iters: 5 iters: 20 agg iters: 1 validation: 1 graph: 0
    #
    # Using devices
    #  Rank  0 Group  0 Pid 550397 on    host1 device  0 [0x1b] NVIDIA H100 80GB HBM3
    #  Rank  1 Group  0 Pid 550398 on    host1 device  1 [0x43] NVIDIA H100 80GB HBM3
    #  Rank  2 Group  0 Pid 550399 on    host1 device  2 [0x52] NVIDIA H100 80GB HBM3
    #  Rank  3 Group  0 Pid 550400 on    host1 device  3 [0x61] NVIDIA H100 80GB HBM3
    #  Rank  4 Group  0 Pid 550401 on    host1 device  4 [0x9d] NVIDIA H100 80GB HBM3
    #  Rank  5 Group  0 Pid 550402 on    host1 device  5 [0xc3] NVIDIA H100 80GB HBM3
    #  Rank  6 Group  0 Pid 550403 on    host1 device  6 [0xd1] NVIDIA H100 80GB HBM3
    #  Rank  7 Group  0 Pid 550405 on    host1 device  7 [0xdf] NVIDIA H100 80GB HBM3
    #  Rank  8 Group  0 Pid 530748 on    host2 device  0 [0x1b] NVIDIA H100 80GB HBM3
    #  Rank  9 Group  0 Pid 530749 on    host2 device  1 [0x43] NVIDIA H100 80GB HBM3
    #  Rank 10 Group  0 Pid 530750 on    host2 device  2 [0x52] NVIDIA H100 80GB HBM3
    #  Rank 11 Group  0 Pid 530751 on    host2 device  3 [0x61] NVIDIA H100 80GB HBM3
    #  Rank 12 Group  0 Pid 530752 on    host2 device  4 [0x9d] NVIDIA H100 80GB HBM3
    #  Rank 13 Group  0 Pid 530755 on    host2 device  5 [0xc3] NVIDIA H100 80GB HBM3
    #  Rank 14 Group  0 Pid 530757 on    host2 device  6 [0xd1] NVIDIA H100 80GB HBM3
    #  Rank 15 Group  0 Pid 530759 on    host2 device  7 [0xdf] NVIDIA H100 80GB HBM3
    #
    #                                                              out-of-place                       in-place
    #       size         count      type   redop    root     time   algbw   busbw #wrong     time   algbw   busbw #wrong
    #        (B)    (elements)                               (us)  (GB/s)  (GB/s)            (us)  (GB/s)  (GB/s)
    268435456      67108864     float     sum      -1   1291.8  207.79  389.61      0   1285.9  208.76  391.42      0
    536870912     134217728     float     sum      -1   2375.3  226.02  423.79      0   2363.8  227.12  425.85      0
    1073741824     268435456     float     sum      -1   4490.8  239.10  448.31      0   4495.1  238.87  447.88      0
    2147483648     536870912     float     sum      -1   8747.2  245.51  460.32      0   8731.6  245.95  461.15      0
    4294967296    1073741824     float     sum      -1    17316  248.03  465.05      0    17267  248.74  466.39      0
    8589934592    2147483648     float     sum      -1    34417  249.59  467.97      0    34390  249.78  468.34      0
    # Out of bounds values : 0 OK
    # Avg bus bandwidth    : 443.007
    #
    ```