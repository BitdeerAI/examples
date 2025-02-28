# Single Node NCCL Test
It is a simple test that runs a simple allreduce operation on a set of GPUs.

## How to run
1. Please check GPU drivers have been installed and the GPUs are visible to the system.

    Please run `nvidia-smi`
    ```
    +-----------------------------------------------------------------------------------------+
    | NVIDIA-SMI 550.90.07              Driver Version: 550.90.07      CUDA Version: 12.4     |
    |-----------------------------------------+------------------------+----------------------+
    | GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
    |                                         |                        |               MIG M. |
    |=========================================+========================+======================|
    |   0  NVIDIA H100 80GB HBM3          On  |   00000000:1B:00.0 Off |                    0 |
    | N/A   25C    P0             69W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   1  NVIDIA H100 80GB HBM3          On  |   00000000:43:00.0 Off |                    0 |
    | N/A   28C    P0             70W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   2  NVIDIA H100 80GB HBM3          On  |   00000000:52:00.0 Off |                    0 |
    | N/A   30C    P0             69W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   3  NVIDIA H100 80GB HBM3          On  |   00000000:61:00.0 Off |                    0 |
    | N/A   29C    P0             72W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   4  NVIDIA H100 80GB HBM3          On  |   00000000:9D:00.0 Off |                    0 |
    | N/A   28C    P0             69W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   5  NVIDIA H100 80GB HBM3          On  |   00000000:C3:00.0 Off |                    0 |
    | N/A   26C    P0             69W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   6  NVIDIA H100 80GB HBM3          On  |   00000000:D1:00.0 Off |                    0 |
    | N/A   29C    P0             72W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    |   7  NVIDIA H100 80GB HBM3          On  |   00000000:DF:00.0 Off |                    0 |
    | N/A   29C    P0             68W /  700W |       1MiB /  81559MiB |      0%      Default |
    |                                         |                        |             Disabled |
    +-----------------------------------------+------------------------+----------------------+
    ```
2. Please check the nvcc version need to match with the installed CUDA version.

    Please run `nvcc --version`
    ```
    nvcc: NVIDIA (R) Cuda compiler driver
    Copyright (c) 2005-2024 NVIDIA Corporation
    Built on Thu_Mar_28_02:18:24_PDT_2024
    Cuda compilation tools, release 12.4, V12.4.131
    Build cuda_12.4.r12.4/compiler.34097967_0
    ```
    if the nvcc command is not found, please check the path here
    Please run `ll /usr/local/`
    ```
    cuda -> /etc/alternatives/cuda/
    cuda-12 -> /etc/alternatives/cuda-12/
    cuda-12.4/
    ```
    if the path exists, please add the path to the environment variable.
    ```
    echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
    echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
    source ~/.bashrc
    ```
    if the path not exists, please follow the [CUDA installation guide](https://developer.nvidia.com/cuda-12-4-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_network) to install the CUDA toolkit.
    ```
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
    sudo dpkg -i cuda-keyring_1.1-1_all.deb
    sudo apt-get update
    sudo apt-get -y install cuda-toolkit-12-4
    ```
    
3. Please check the nccl has been installed.

    Please run `ldconfig -p | grep nccl`
    ```
    libnccl.so.2 (libc6,x86-64) => /lib/x86_64-linux-gnu/libnccl.so.2
	libnccl.so (libc6,x86-64) => /lib/x86_64-linux-gnu/libnccl.so
    ```
    if the nccl library not found
    Please run `apt install -y libnccl2 libnccl-dev`

4. Then git clone the nccl-tests repository and build the test.

    ```
    git clone https://github.com/NVIDIA/nccl-tests
    cd nccl-tests
    make
    ```

5. Run the following command to run the test.
    ```
    ./build/all_reduce_perf -b 256M -e 2G -f 2 -g 8
    ```

    ```
    # nThread 1 nGpus 8 minBytes 268435456 maxBytes 2147483648 step: 2(factor) warmup iters: 5 iters: 20 agg iters: 1 validation: 1 graph: 0
    #
    # Using devices
    #  Rank  0 Group  0 Pid 1623025 on    host device  0 [0x1b] NVIDIA H100 80GB HBM3
    #  Rank  1 Group  0 Pid 1623025 on    host device  1 [0x43] NVIDIA H100 80GB HBM3
    #  Rank  2 Group  0 Pid 1623025 on    host device  2 [0x52] NVIDIA H100 80GB HBM3
    #  Rank  3 Group  0 Pid 1623025 on    host device  3 [0x61] NVIDIA H100 80GB HBM3
    #  Rank  4 Group  0 Pid 1623025 on    host device  4 [0x9d] NVIDIA H100 80GB HBM3
    #  Rank  5 Group  0 Pid 1623025 on    host device  5 [0xc3] NVIDIA H100 80GB HBM3
    #  Rank  6 Group  0 Pid 1623025 on    host device  6 [0xd1] NVIDIA H100 80GB HBM3
    #  Rank  7 Group  0 Pid 1623025 on    host device  7 [0xdf] NVIDIA H100 80GB HBM3
    #
    #                                                              out-of-place                       in-place
    #       size         count      type   redop    root     time   algbw   busbw #wrong     time   algbw   busbw #wrong
    #        (B)    (elements)                               (us)  (GB/s)  (GB/s)            (us)  (GB/s)  (GB/s)
    268435456      67108864     float     sum      -1   1119.8  239.71  419.49      0   1117.4  240.24  420.42      0
    536870912     134217728     float     sum      -1   2157.5  248.83  435.46      0   2154.8  249.16  436.02      0
    1073741824     268435456     float     sum      -1   4027.3  266.61  466.57      0   4029.1  266.50  466.37      0
    2147483648     536870912     float     sum      -1   7983.5  268.99  470.73      0   7985.2  268.93  470.63      0
    # Out of bounds values : 0 OK
    # Avg bus bandwidth    : 448.212
    #
    ```
    