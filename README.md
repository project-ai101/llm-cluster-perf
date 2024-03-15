# llm-cluster-perf
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -- Author: Bin Tan

Cluster level LLM training and inference performance experiements. Both LLM training and inference are critical and expansive. Here are some interesting 
experiments and results.

For tutorial on LLM training and inference infrastructure, one may refere to 
[my LLM Infrastructure](https://github.com/project-ai101/llm-infra/tree/main) reposity.

For tutorial on low level AI HPC computation with CUDA, one may refere to 
[my AI HPC with CUDA](https://github.com/project-ai101/ai-hpc-with-cuda)
repository.

### Design 1: Llama 2 Inference Performance with Three Low Cost GPUs over Three Linux Nodes
##### -- Test 1.1
```
   Timestamp:
      March 13, 2024
```

```
   AI HPC Cluster Configuration: 
     Three Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Three Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1000
```

```
   Performance: 
     1. With batch size 1, Llama-2 13B model (16-bits float model, no quantization) can
        generate 7 tokens per seconds.
     2. Each token generation take about 0.1 seconds in GPU computation and about 0.04
        seconds in others.
```

```
   Analysis:
     1. Llama-2 13B model needs more than 26GB onboard GPU DRAM to run efficient. So, used
        three Nvidia RTX 3060 as each of them has only 12GB onboard GPU DRAM.
     2. Data movement takes ~30%. Not bad. But there are rooms to improve.
     3. Need to try if the cluster throughput increases while increase the batch size.
     4. Need to explore the cluster performance scalability with respect to the individual
        GPU computation capacity.
```
##### -- Test 1.2
When launch a request with a large batch size, out of memory issues occurred in the GPU cards. A simple
trick for Llama 2 model is to configure the maximum sequence length to be small and meanwhile reduce the maximum 
generation length. Then, run with batch size from 1 to 8. The token generation output of a Llama-2 13B model
demonstrated a linear scalability in the configuration. Here is the data

| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      1     |          7.5                     |
|      2     |         15.7                     |
|      3     |         22.8                     |
|      4     |         30.1                     |
|      5     |         39.0                     |
|      6     |         47.5                     |
|      7     |         53.5                     |
|      8     |         62.1                     |
