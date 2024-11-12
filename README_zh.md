# SRA-MCTS: Self-driven Reasoning Aurmentation with Monte Carlo Tree Search for Enhanced Code Generation
这个仓库是为论文(xxx)构造的.如果您有任何问题或建议,请随时告诉我们.您可以使用电子邮箱()联系我们,或者直接在此仓库上发布问题.

# Overview
在这项工作中,我们提出并验证
1. 小型模型通过自我生成的数据，其性能提升能力可达到甚至超过由大型模型蒸馏得到的数据带来的性能提升.
2. SRA-MCTS 生成的数据比通过 CoT 方式生成的数据更加多样化。实验验证表明，SRA-MCTS 在提升模型性能方面超越了 CoT 方法.

总体流程如下:
![pipeline](https://github.com/user-attachments/assets/07c5c70a-d908-4a63-90bf-248fad01c85b)

通过广泛的实验,我们发现:
1. SRA-MCTS提升了小模型的自主推理能力，使自我生成的数据相比于70B模型蒸馏数据带来更大的性能提升。
2. SRA-MCTS在平均性能上超越了CoT，且在反映多样性的pass@10上表现尤为出色。
3. 在小模型上，SRA-MCTS主要提升多样性；随着模型增大，SRA-MCTS同时展现多样性和可靠性。


# Language Models and Datasets
我们在gemma-2-2b、Meta-Llama-3.1-8B和Qwen2.5-14B上进行了实验
使用了代码领域的评估数据集,human-eval、humaneval+和MBPP、MBPP+

# Boardline
使用SRA-MCTS与其他模型进行比较
|  Model | Size  | Human-eval  | Human-eval+  | MBPP  | MBPP+  |
|---|---|---|---|---|---|
|  gemma-2-2b-Instruct  |2B| 39.76  | 33.05  | 34.42  | 43.39  | 
| gemma-2-2b-CoT  | 2B  | 41.89  | 35.37  | 34.90  |43.70   |
|  **gemma-2-2b-SRA-MCTS** | 2B  | 40.73 | 34.88  | 33.92  | 45.37  |
|  CodeGen-2B | 2B  | 24.4 |22.6  |  46.3  |36    |
| CodeT5+-2B | 2B  |25   |22   | 48.4  |38.1   |
| codegemma-2b | 2B  | 26.8  |20.7   | 55.6  |46.6   |
|||||||
| Meta-Llama-3.1-8B-Instruct | 8B  | 62.74  |58.90   |51.94   |45.37   |
| Meta-Llama-3.1-8B-CoT |  8B | 62.32  |58.35   |52.94   |60.50   |
| **Meta-Llama-3.1-8B-SRA-MCTS** | 8B | 62.19  |57.87   |54.52   |59.97   |
| Zephyr β-7B | 7B  | 30  |23.2   | 42.1  |34.7   |
| Mistral-7B | 7B  | 28.7  |23.8   | 51.9  |42.1   |
| gemma-7b |  7B |  35.4 |28.7   | 52.6  |43.4   |
| CodeT5+-6B | 6B | 29.3  |24.4   | 52.9  |41.5   |
| WizardCoder-Python-7B-V1.0 | 7B  | 50.6  |45.1   |  58.5 |49.5   |
| CodeLlama-7B |  7B |  37.8 |35.4   | 59.5  |46.8   |
| codegemma-7b | 7B  | 44.5  |41.5   | 65.1  |52.4   |
| DeepSeek-Coder-6.7B-Instruct | 6.7B  | 74.4  |71.3   | 74.9|65.6|
| CodeQwen1.5-7B |  7B | 51.8  |45.7   | 73.5  |60.8|
| Magicoder-S-DS-6.7B | 6.7B  | 76.8  |71.3   | 79.4  |69|
|||||||
| Qwen2.5-14B-Instruct | 14B  | 80.37  |76.52   |56.42   |61.48   |
| Qwen2.5-14B-CoT |  14B | 78.66  |73.84   |58.12   |63.97   |
| **Qwen2.5-14B-SRA-MCTS** |  14B |85.37   |75.00   |61.02   |61.16   |
| CodeGen-16B | 16B  | 32.9  |28   | 54.2  |45.5   |
| StarCoder-15B | 15B  | 34.1  |29.3   |55.1   |46.1   |
| CodeT5+-16B | 16B  | 31.7  |26.8   | 56.6  |47.1   |
| CodeLlama-13B | 13B  |42.7   |38.4   |63.5   |52.6   |
| WizardCoder-15B-V1.0 | 15B  | 56.7  |50.6   | 64.3  |54.2   |


# code
我们的代码修改自(https://github.com/THUDM/ReST-MCTS)
