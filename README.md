# llm-cluster-perf
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -- Author: Bin Tan

Cluster level LLM training and inference performance experiements. Both LLM training and inference are critical and expansive. Here are some interesting 
experiments and results.

For tutorial on LLM training and inference infrastructure, one may refere to 
[my LLM Infrastructure](https://github.com/project-ai101/llm-infra/tree/main) reposity.

For tutorial on low level AI HPC computation with CUDA, one may refere to 
[my AI HPC with CUDA](https://github.com/project-ai101/ai-hpc-with-cuda)
repository.

### Performance Tuning Case 1

```
   AI HPC Cluster Configuration: 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. The LLM model used is Llama2-13B.
```

```
   Total GPU Cost: < $1200
```

The performance was tuned from 7.5 tokens/second with patch size 1 to 386.4 tokens/second with batch size 96. 
For the details of the performance tuning journey, one may refer to [perf_tune_1.md](https://github.com/project-ai101/llm-cluster-perf/blob/main/perf_tune_1.md)
