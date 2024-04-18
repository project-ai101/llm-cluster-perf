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


### Design 2: Llama 2 Inference Performance with Four Low Cost GPUs over Four Linux Nodes
As experienced in Design 1 above, the GPU VRAM is a bottleneck for a large batch size. This is a quite well known issues.
One solution is scale-up the GPU card with large size VRAM, such as Nvidia H100. But it is very expensive and even hard to 
buy one from manufactures or rent one from cloud providers. Then, the second solutino is scale-out, which I use here. 
The cluster I have does demonstrate a very good scalability. By adding one node with one RTX 3060, both computation capacity
and GPU VRAM memory size increase 33.33%. Now, we 48GB GPU VRAM. Then, a few large size batches had been performed in the
following Test 2.1

##### -- Test 2.1
```
   Timestamp:
      March 25, 2024
```

```
   AI HPC Cluster Configuration: 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Four Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1200
```
```
   Performance Results:
```
| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      12    |         51.82                    |
-------------------------------------------------
|      16    |         85.66                    |
-------------------------------------------------
|      20    |         92.63                    |
-------------------------------------------------

```
   Analysis:
     1. The total performance increasing is (92.63 - 62.1) / 62.1 = 30.53 / 62.1 = 49.16%. With 33.33% cost increasing by
        adding addtional node with one RTX 3060, we have 49.16% throughput increaseing. A good deal!
     2. With batch size 12, the total output is even smaller than batch size 8 in the Three GPUs cluster in Design 1. Additional
        node introdues additional communication cost.
     3. For large size batches like 16 and 20, the computation time plays dominate role while the communication cost is
        almost fixed. Therefore, the throughput increases a lot.
     4. Any further throughput improvement without increasing cost?

```


##### -- Test 2.2
```
   Timestamp:
      April 10, 2024
```

```
   AI HPC Cluster Configuration (the same as in Test 2.1 above): 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Four Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1200
```
```
   Performance Results:
```
Managed to be able to run batch size of 24. The performance increased 28.8% with respect to batch size 20 in test 2.1.

| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      24    |         119.31                   |

##### -- Test 2.3
```
   Timestamp:
      April 12, 2024
```

```
   AI HPC Cluster Configuration (the same as in Test 2.1 above): 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Four Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1200
```
```
   Performance Results:
```
Now, the same HPC cluster configuration can run 32 batch size whith 189.44 tokens/second throughput. 
The performance increased (189.44 - 119.31) / 119.31 = 58.8% with respect to batch size 24 in test 2.2.

| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      32    |         189.44                   |

##### -- Test 2.4
```
   Timestamp:
      April 12, 2024
```

```
   AI HPC Cluster Configuration (the same as in Test 2.1 above): 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Four Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1200
```
```
   Performance Analysis and Results:
```
As we experienced before, one method to increase the throughput is to increase the batch size. However, the batch size is
limitted by the GPU VRAM size. For the configuration, finally managed to run a test with a batch size of 48. And the result
is 
| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      48    |         209.76                   |

The percentage of the batch size increaseing is (48 - 32) / 32 = 50% but the overall throughput is just increased by
(209.76 - 189.44) / 189.44 = 10.72%. This indicates that we hit on some either hardware bottleneck or software implementation
bottleneck.

One may question why don't we use a single GPU card with large VRAM. The main reason is the cost. A single GPU card with 
VRAM size 40GB or bigger normaly costs more than $4K. 

From Test 2.1 to Test 2.4, the total throughput has been increased from 7.5 tokens/second with batch size 1 for to Llama 2 13B model to
209.76 tokens/second with batch size 48

##### -- Test 2.5
```
   Timestamp:
      April 17, 2024
```

```
   AI HPC Cluster Configuration (the same as in Test 2.1 above): 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. Four Linux boxes are connected by an Ethernet switch.
```

```
   Total GPU Cost: < $1200
```
```
   Performance Results:
```
The difference in the prompt configuration are max_seq_len = 256 and max_gen_len = 220 instead of max_seq_len = 512 and max_gen_len = 256 
above tests from Test 2.1 to Test 2.4. The results are


| Batch Size | Token Throughput (tokens/second) |
|------------|----------------------------------|
|      48    |         232.92                   |
|      64    |         335.36                   |
|      80    |         371.2                    |
|      96    |         386.4                    |


The performance improvement from patch size 80 to patch size 96 is small. The reason behind is that the GPU computation is saturated.
