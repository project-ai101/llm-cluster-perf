# LLM Cluster 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -- Author: Bin Tan, [my linkedin](https://www.linkedin.com/in/bin-tan-3145721/)

LLM training, fine-tuning and inference typically involves many servers with tens or even thousands of GPUs. To design a cluster, three layers of works are typically required to be studied,

- Physical cluster design including hardware (CPUs, GPUs, Switches, DRAM, Motherboards, etc) selection and networking topology design. The [AI HPC with CUDA](https://github.com/project-ai101/ai-hpc-with-cuda) tutorial repository gives deep dive in GPU hardware and performance.
- LLM Infrastructure software.  [LLM Infrastructure](https://github.com/project-ai101/llm-infra/tree/main) reposity have lots of insight information in this category.
- LLM service access framework.

In this tutorial repository, I mainly focus on two topics. 
- Information on open source LLM service access frameworks.
- Performance data from some small experienment clusters I built.

## LLM Service Access Framework
#### LangChain Framework
LangChain is a framework to help developers to develop and deploy LLM based Rest APIs services. It has three pilars, Open-source libraries, Productionization with LanSmith and Deployment with LangServer. For details, one may refer to its [github link](https://github.com/langchain-ai/langchain).

#### Microsoft Semantic-Kernel
Microsoft semantic-kernel is a lightweight LLM service access framework. It supports C#, Python and Java. It is easy to install and use due to simplicity. The github link is [here](https://github.com/microsoft/semantic-kernel/tree/main)

## LLM Cluster Performance
Cluster level LLM training and inference performance experiements. Both LLM training and inference are critical and expansive. Here are some interesting 
experiments and results.

### Performance Tuning Case 1

```
   AI HPC Cluster Configuration: 
     Four Nvidia RTX 3060 GPU cards. Each card has only 12GB onboard DRAM and is installed
     on a separate Linux box. The LLM model used is Llama2-13B.
```

```
   Total GPU Cost: < $1200
```

#### Results
The performance was tuned from 7.5 tokens/second with patch size 1 to 386.4 tokens/second with batch size 96. 
For the details of the performance tuning journey, one may refer to [perf_tune_1.md](https://github.com/project-ai101/llm-cluster-perf/blob/main/perf_tune_1.md)


### Build A GPU Commodity Cluster to Concurrently Run Multiple LLM Models
```
   Timestamp:
      April 30, 2024
```
```
   AI HPC Cluster Configuration: 
     2 Linux Servers
       Four Nvidia RTX 3060 GPU cards,
       Two Nvidia RTX 4060 TI Super GPU Cards,
       One Nvidia RTX 4070 TI Super GPU Card.
     Total GPU VRAM: 96GB 
```

```
   Total GPU Cost: < $3000
```

```
   LLM Models concurrently running:
      1 Llama2 7B
      1 Llama2 13B
      1 Llama3 8B
      1 CodeLlama 34B Python

   Note: The conventional way to run all four models concurrently requires
         more than 130GB GPU VRAM. 
```
