# NCCL Test
It is a simple test that runs a simple allreduce operation on a set of GPUs. You could run it on a single node or multiple nodes.

## How to run

### Single Node
Please follow the [nccl-tests](https://github.com/NVIDIA/nccl-tests) documentation to install the required dependencies. After that, you can run the following command to run the test.

Inside the `nccl-tests` directory, run the following command:
```bash
./build/all_reduce_perf -b 256M -e 8G -f 2 -g 8
```
### Multi Node
Make sure you have the same version of NCCL installed on all the nodes. You can run the following command to run the test.  

Apart from the dependencies of nccl-tests, you also need to make sure all the nodes have passwordless SSH access to each other. You can follow the [SSH documentation](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/) to set up passwordless SSH. MPI is also required to run the test on multiple nodes.

After all the dependencies are installed, you can run the following command to run the test.
```bash
NCCL_DEBUG=Info mpirun --allow-run-as-root --display-allocation -np 2 -H IP of 1st node,IP of 2nd node -x NCCL_SOCKET_IFNAME=Network Interfaces  -x NCCL_IB_HCA=mlx5 ./build/all_reduce_perf -b 256M -e 8G -f 2 -g 8
```
In the above command, replace the `IP of 1st node`, `IP of 2nd node`, and `Network Interfaces` with the actual values.
Example:
```bash
NCCL_DEBUG=Info mpirun --allow-run-as-root --display-allocation -np 2 -H 192.168.0.3,192.168.0.4 -x NCCL_SOCKET_IFNAME=eth  -x NCCL_IB_HCA=mlx5 ./build/all_reduce_perf -b 256M -e 8G -f 2 -g 8
```
