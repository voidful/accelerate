<!---
Copyright 2021 The HuggingFace Team. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# In this folder we showcase various full examples using 🤗 Accelerate

## Simple NLP example

The [nlp_example.py](./nlp_example.py) script is a simple example to train a Bert model on a classification task ([GLUE's MRPC]()).

The same script can be run in any of the following configurations:
- single CPU or single GPU
- multi GPUS (using PyTorch distributed mode)
- (multi) TPUs
- fp16 (mixed-precision) or fp32 (normal precision)

To run it in each of these various modes, use the following commands:
- single CPU:
    * from a server without GPU
        ```bash
        python ./nlp_example.py
        ```
    * from any server by passing `cpu=True` to the `Accelerator`.
        ```bash
        python ./nlp_example.py --cpu
        ```
    * from any server with Accelerate launcher
        ```bash
        accelerate launch --cpu ./nlp_example.py
        ```
- single GPU:
    ```bash
    python ./nlp_example.py  # from a server with a GPU
    ```
- with fp16 (mixed-precision)
    * from any server by passing `fp16=True` to the `Accelerator`.
        ```bash
        python ./nlp_example.py --fp16
        ```
    * from any server with Accelerate launcher
        ```bash
        accelerate launch --fb16 ./nlp_example.py
- multi GPUS (using PyTorch distributed mode)
    * With Accelerate config and launcher
        ```bash
        accelerate config  # This will create a config file on your server
        accelerate launch ./nlp_example.py  # This will run the script on your server
        ```
    * With traditional PyTorch launcher
        ```bash
        python -m torch.distributed.launch --nproc_per_node 2 --use_env ./nlp_example.py
        ```
- multi GPUs, multi node (several machines, using PyTorch distributed mode)
    * With Accelerate config and launcher, on each machine:
        ```bash
        accelerate config  # This will create a config file on each server
        accelerate launch ./nlp_example.py  # This will run the script on each server
        ```
    * With PyTorch launcher only
        ```bash
        python -m torch.distributed.launch --nproc_per_node 2 \
            --use_env \
            --node_rank 0 \
            --master_addr master_node_ip_address \
            ./nlp_example.py  # On the first server
        python -m torch.distributed.launch --nproc_per_node 2 \
            --use_env \
            --node_rank 1 \
            --master_addr master_node_ip_address \
            ./nlp_example.py  # On the second server
        ```
- (multi) TPUs
    * With Accelerate config and launcher
        ```bash
        accelerate config  # This will create a config file on your TPU server
        accelerate launch ./nlp_example.py  # This will run the script on each server
        ```
    * In PyTorch:
        Add an `xmp.spawn` line in your script as you usually do.
