# llm-cluster-perf
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -- Author: Bin Tan

Cluster level LLM training and inference performance experiements. Both LLM training and inference are critical and expansive. Here are some interesting 
experiments and results.

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

