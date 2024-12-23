# Machine Learning Training

We will be using [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) to train a machine learning model. LLaMA-Factory is a collection of scripts that can be used to train a machine learning model on a single node or multiple nodes. It also supports three distributed training engines: DDP, DeepSpeed, and FSDP.

## How to run
Make sure you have the required dependencies installed. 
```bash
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
pip install -e ".[torch,metrics]"
```

Download the required dataset and model weights, for example, `meta-llama/Meta-Llama-3-8B-Instruct`.

### Single Node
You can run the following command to train a model on a single node.
```bash
FORCE_TORCHRUN=1 llamafactory-cli train machine_learning_training/configs/lora/llama3_lora_sft.yaml
```

### Multi Node
```bash
FORCE_TORCHRUN=1 NNODES=2 NODE_RANK=0 MASTER_ADDR=192.168.0.1 MASTER_PORT=29500 \
llamafactory-cli train machine_learning_training/configs/lora/llama3_lora_sft.yaml

FORCE_TORCHRUN=1 NNODES=2 NODE_RANK=1 MASTER_ADDR=192.168.0.1 MASTER_PORT=29500 \
llamafactory-cli train machine_learning_training/configs/lora/llama3_lora_sft.yaml
```