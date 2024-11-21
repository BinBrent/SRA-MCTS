# SRA-MCTS: 自驱动推理增强与蒙特卡罗树搜索用于增强代码生成
<div align="center">
  <img src="https://github.com/user-attachments/assets/754d13ef-f5fc-4147-8f71-812a006a9400" width="700" height="400" />
</div>
<p align="center">
  📄 <a href="https://arxiv.org/pdf/2411.11053" target="_blank">Paper</a> &nbsp; | &nbsp;
  🤗 <a href="#代码" target="_blank">Quick start</a> &nbsp;
</p>

# 目录
- [概述](#概述)
- [实验结果](#实验结果)
  - [外部模型生成数据和自身生成数据的对比](#外部模型生成数据和自身生成数据的对比)
  - [消融实验](#消融实验)
  - [不同方法的性能对比](不同方法的性能对比)
- [语言模型与数据集](#语言模型与数据集)
- [对比](#对比)
- [代码](#代码)

# 概述
在这项工作中，我们提出并验证：
1. 小型模型通过自我生成的数据，其性能提升能力可达到甚至超过由大型模型蒸馏得到的数据带来的性能提升，与70B模型合成数据的方法相比，SRA-MCTS在2B和8B量级上，Human-Eval和Human-Eval+的指标平均提升了**2**分。
2. SRA-MCTS生成的数据比通过CoT方式生成的数据更加多样化。实验验证表明，SRA-MCTS在提升模型性能方面超越了CoT方法，且在8B和14B量级，分别有着**5**分和**3**分的提升。

总体流程如下：
![pipeline](https://github.com/user-attachments/assets/07c5c70a-d908-4a63-90bf-248fad01c85b)

# 实验结果
通过广泛的实验，我们发现：

1. **SRA-MCTS提升了小模型的自主推理能力**
   - 与70B模型合成数据的方法相比，SRA-MCTS在2B和8B量级上，Human-Eval和Human-Eval+的指标平均提升了**2**分。直到14B这一量级，70B模型合成的数据才出现反超。
   - 相比于70B模型蒸馏数据，SRA-MCTS生成的数据在小模型上带来了更大的性能提升。

2. **SRA-MCTS在平均性能上超越了CoT**
   - SRA-MCTS在除MBPP+外，几乎全线超越了CoT方法，并且在Human-Eval+上与Instruct只有不到2分的差距。
   - 计算各个模型在各个benchmark的增量平均值，SRA-MCTS在所有量级上均有性能提升，且在8B和14B量级，分别有着**5**分和**3**分的提升。

3. **SRA-MCTS在反映多样性的pass@10上表现尤为出色**
   - SRA-MCTS在pass@10上的表现，尤其是在多次生成任务中，显著优于CoT方法，尤其是在小模型上表现出较强的多样性。

4. **SRA-MCTS随着模型增大，展现多样性与可靠性并存的优势**
   - 在小模型上，由于中间评估能力不足，SRA-MCTS主要提升了多样性，提升主要体现在pass@10这一多次生成的方式上。
   - 随着模型规模增大，指令遵循能力和评估能力的提高，SRA-MCTS不仅在pass@10上表现优异，在pass@1上也出现了反超，表现出多样性和可靠性的双重优势。

### 外部模型生成数据和自身生成数据的对比

<p align="center">
  <img width="500" alt="62b86fc1ed018e717e1ef1ae806d88e" src="https://github.com/user-attachments/assets/b8d78db2-5c08-40a9-b24b-75ee5018de58">
</p>

上图展示了同一模型在自生成数据与由外部模型蒸馏得到数据上的性能对比。在此对比中，外部模型使用的是 **Meta-Llama-3-70B-Instruct**。

### 消融实验

![ablation](https://github.com/user-attachments/assets/86be3459-c9ca-45d1-8f0c-30e508c3cde3)

该实验研究了自然语言推理步骤在模型回答中的作用，其中 **SRA-MCTS** 的训练数据包含自然语言推理与代码，另一组数据则只包含代码。

我们发现，去除自然语言数据训练的模型在所有三个模型规模类别中均表现出了明显的性能下降。尽管在 **Human-Eval** 相关的benchmark上，性能差距较小，仅为1-2分左右，但在 **MBPP** 相关的benchmark上，性能差距显著。

- 在 **2B** 模型上，**MBPP+** 上的性能差距接近 **7** 分，其他 benchmark 上也有约 **1-2** 分的差距。
- 在 **8B** 模型上，**MBPP** 上的性能差距高达 **7** 分。
- 在 **14B** 模型的 **MBPP(pass@10)** 中，性能差距最大，达到了 **13** 分。

这充分说明了自然语言推理对模型思维的引导和刺激作用。

### 不同方法的性能对比

<p align="center">
  <img width="694" alt="e6b067489885e4de46dac0b2f8b15a9" src="https://github.com/user-attachments/assets/39ebe376-81e7-47e3-b57b-c2f8687668d5">
</p>

上表对比了 **官方发布的指令版本**、**CoT 训练版本** 和我们提出的 **SRA-MCTS** 在 **2B**、**8B** 和 **14B** 规模下的性能表现。表中标有 **`*`** 和 **加粗** 的值表示在特定基准测试中，该模型在该规模类别下表现最优。


# 语言模型与数据集
我们在gemma-2-2b、Meta-Llama-3.1-8B和Qwen2.5-14B上进行了实验，使用了代码领域的评估数据集：human-eval、human-eval+和MBPP、MBPP+。

# 对比
使用SRA-MCTS与其他模型进行比较

|  模型  | 规模  | Human-eval  | Human-eval+  | MBPP  | MBPP+  |
|---|---|---|---|---|---|
|  [gemma-2-2b-Instruct](https://huggingface.co/google/gemma-2-2b-it)  |2B| 39.76  | 33.05  | 34.42  | 43.39  | 
| gemma-2-2b-CoT  | 2B  | 41.89  | 35.37  | 34.90  |43.70   |
|  **gemma-2-2b-SRA-MCTS** | 2B  | 40.73 | 34.88  | 33.92  | 45.37  |
|  [CodeGen-2B](https://arxiv.org/abs/2203.13474) | 2B  | 24.4 |22.6  |  46.3  |36    |
| [CodeT5+-2B](https://www.salesforce.com/blog/codet5/) | 2B  |25   |22   | 48.4  |38.1   |
| [codegemma-2b](https://huggingface.co/google/codegemma-2b) | 2B  | 26.8  |20.7   | 55.6  |46.6   |
|||||||
| [Meta-Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct) | 8B  | 62.74  |58.90   |51.94   |45.37   |
| Meta-Llama-3.1-8B-CoT |  8B | 62.32  |58.35   |52.94   |60.50   |
| **Meta-Llama-3.1-8B-SRA-MCTS** | 8B | 62.19  |57.87   |54.52   |59.97   |
| [Zephyr β-7B](https://huggingface.co/HuggingFaceH4/zephyr-7b-beta) | 7B  | 30  |23.2   | 42.1  |34.7   |
| [Mistral-7B](https://mistral.ai/news/announcing-mistral-7b/) | 7B  | 28.7  |23.8   | 51.9  |42.1   |
| [gemma-7b](https://huggingface.co/google/gemma-7b) |  7B |  35.4 |28.7   | 52.6  |43.4   |
| [CodeT5+-6B](https://www.salesforce.com/blog/codet5/) | 6B | 29.3  |24.4   | 52.9  |41.5   |
| [WizardCoder-Python-7B-V1.0](https://huggingface.co/WizardLM/WizardCoder-Python-7B-V1.0) | 7B  | 50.6  |45.1   |  58.5 |49.5   |
| [CodeLlama-7B](https://about.fb.com/news/2023/08/code-llama-ai-for-coding/amp/) |  7B |  37.8 |35.4   | 59.5  |46.8   |
| [codegemma-7b](https://huggingface.co/google/codegemma-7b) | 7B  | 44.5  |41.5   | 65.1  |52.4   |
| [DeepSeek-Coder-6.7B-Instruct](https://huggingface.co/deepseek-ai/deepseek-coder-6.7b-instruct) | 6.7B  | 74.4  |71.3   | 74.9|65.6|
| [CodeQwen1.5-7B](https://huggingface.co/Qwen/CodeQwen1.5-7B) |  7B | 51.8  |45.7   | 73.5  |60.8|
| [Magicoder-S-DS-6.7B](https://huggingface.co/ise-uiuc/Magicoder-S-DS-6.7B) | 6.7B  | 76.8  |71.3   | 79.4  |69|
|||||||
| [Qwen2.5-14B-Instruct](https://huggingface.co/Qwen/Qwen2.5-14B-Instruct) | 14B  | 80.37  |76.52   |56.42   |61.48   |
| Qwen2.5-14B-CoT |  14B | 78.66  |73.84   |58.12   |63.97   |
| **Qwen2.5-14B-SRA-MCTS** |  14B |85.37   |75.00   |61.02   |61.16   |
| [CodeGen-16B](https://arxiv.org/abs/2203.13474) | 16B  | 32.9  |28   | 54.2  |45.5   |
| [StarCoder-15B](https://huggingface.co/bigcode/starcoder) | 15B  | 34.1  |29.3   |55.1   |46.1   |
| [CodeT5+-16B](https://www.salesforce.com/blog/codet5/) | 16B  | 31.7  |26.8   | 56.6  |47.1   |
| [CodeLlama-13B](https://about.fb.com/news/2023/08/code-llama-ai-for-coding/amp/) | 13B  |42.7   |38.4   |63.5   |52.6   |
| [WizardCoder-15B-V1.0](https://huggingface.co/WizardLM/WizardCoder-15B-V1.0) | 15B  | 56.7  |50.6   | 64.3  |54.2   |

# 代码
下面提供了快速开始的步骤
### 创建虚拟环境
  ```bash
  conda create --name SRA-MCTS python=3.10
  conda activate SRA-MCTS
  pip install requirements.txt
  ```
### 运行步骤
1. 在models/model.py中line 6，填写您希望使用的模型路径（推荐使用绝对路径）
```
from models.inference_models import get_inference_model, get_local_response # , get_vllm_infer_model, get_local_response_vllm
from models.value_models import get_value_model, get_local_value # , get_vllm_value_model, get_local_value_vllm

path = 'path/to/model'

INFERENCE_MODEL_DIR = path
```
3. 在希望运行的入口（cot.py,tot.py,mcts.py）中设置您希望使用的数据路径(格式为只有question字段的json文件),假设为q.json
```
with open('path/to/q.json', 'r') as f: # Your question files
    lines = f.readlines()

model = 'qwen-0.5'
reasoning = open('data/'+ model + '-reasoning' + '.json', 'w', encoding='utf-8')
output_file = 'output-' + model + '.log'
   ```
设置完之后
  ```bash
  CUDA_VISIBLE_DEVICES=0,1 python cot.py 
  ```
5. 运行完毕后，将在data/reasoning中获得推理结果（自然语言），运行data/clean.ipynb进行数据过滤，先去掉内容中的具体代码，再过滤掉字符数过少的行，结果形式为(question，solution)两个字段的json文件,例如:
   ```
   {"question": "Given the array `queries` of positive integers between `1` and `m`, you have to process all `queries[i]` (from `i=0` to `i=queries.length-1`) according to the following rules:\n\n*   In the beginning, you have the permutation `P=[1,2,3,...,m]`.\n*   For the current `i`, find the position of `queries[i]` in the permutation `P` (**indexing from 0**) and then move this at the beginning of the permutation `P.` Notice that the position of `queries[i]` in `P` is the result for `queries[i]`.\n\nReturn an array containing the result for the given `queries`.\n\n", "solution": "Step 1: Initialize the map `pos` with keys as numbers from 1 to m and values as their initial positions in the permutation P (which are their indices in the range from 0 to m-1).\nStep 2: Iterate over each query in the array `queries`, and for each query `queries[i]`, find its position in the permutation `P` by looking up its value in the `pos` map, then update the permutation `P` by removing the query from its current position and inserting it at the beginning of `P`, updating the `pos` map accordingly.\n"}
   ```
7. 使用data/translator.ipynb，对过滤的文件进行代码生成，在nl2sl文件中得到代码结果
```
{"question": "### Question: A company has `n` employees with a unique ID for each employee from `0` to `n - 1`. The head of the company is the one with `headID`.\n\nEach employee has one direct manager given in the `manager` array where `manager[i]` is the direct manager of the `i-th` employee, `manager[headID] = -1`. Also, it is guaranteed that the subordination relationships have a tree structure.\n\nThe head of the company wants to inform all the company employees of an urgent piece of news. He will inform his direct subordinates, and they will inform their subordinates, and so on until all employees know about the urgent news.\n\nThe `i-th` employee needs `informTime[i]` minutes to inform all of his direct subordinates (i.e., After informTime\\[i\\] minutes, all his direct subordinates can start spreading the news).\n\nReturn _the number of minutes_ needed to inform all the employees about the urgent news.\n\n\n### Solution: \nStep 1: Perform a depth-first search (DFS) from the head of the company to traverse the tree structure of the company and calculate the minimum time required for each employee to inform their direct subordinates.\n\n", "solution": "### Problem Solution: Inform All Employees\n\nWe can solve this problem using a depth-first search (DFS) approach. Here's the step-by-step implementation:\n\n```python\nfrom collections import defaultdict, deque\n\ndef numOfMinutes(n: int, headID: int, manager: list[int], informTime: list[int]) -> int:\n    \"\"\"\n    Calculate the minimum time required to inform all employees about the urgent news.\n\n    Args:\n    n (int): The number of employees.\n    headID (int): The ID of the head of the company.\n    manager (list[int]): A list of direct managers for each employee.\n    informTime (list[int]): A list of time required for each employee to inform their direct subordinates.\n\n    Returns:\n    int: The minimum time required to inform all employees about the urgent news.\n    \"\"\"\n\n    # Create a graph using an adjacency list representation\n    graph = defaultdict(list)\n    for i in range(n):\n        graph[manager[i]].append(i)\n\n    # Initialize the maximum time and the queue for BFS\n    max_time = 0\n    queue = deque([(headID, 0)])  # (employee ID, time)\n\n    while queue:\n        employee, time = queue.popleft()\n        max_time = max(max_time, time)\n        \n        # Add all the subordinates of the current employee to the queue\n        for subordinate in graph[employee]:\n            queue.append((subordinate, time + informTime[subordinate]))\n\n    return max_time\n```\n\n### Example Use Case:\n\n```python\nn = 6\nheadID = 2\nmanager = [2, 2, -1, 2, 2, 2]\ninformTime = [0, 0, 0, 0, 0, 0]\n\nresult = numOfMinutes(n, headID, manager, informTime)\nprint(result)  # Output: 1\n```\n\nIn this example, the head of the company is employee 2, and all employees are connected to employee 2. The time required for each employee to inform their direct subordinates is 0. Therefore, the minimum time required to inform all employees is 1."}
```
9. 在data/piped中运行merged.ipynb，拼接上prompt并且进行shuffle
```
{"question": "Create a structured approach to solve the problem, with each step explained in detail. After that, write the Python code that executes the solution.\nGiven an `m x n` matrix `matrix` and an integer `k`, return _the max sum of a rectangle in the matrix such that its sum is no larger than_ `k`.\n\nIt is **guaranteed** that there will be a rectangle with a sum no larger than `k`.\n\n**Example 1:**\n\n**Input:** matrix = \\[\\[1,0,1\\],\\[0,-2,3\\]\\], k = 2\n**Output:** 2\n**Explanation:** Because the sum of the blue rectangle \\[\\[0, 1\\], \\[-2, 3\\]\\] is 2, and 2 is the max number no larger than k (k = 2).\n\n**Example 2:**\n\n**Input:** matrix = \\[\\[2,2,-1\\]\\], k = 3\n**Output:** 3\n\n**Constraints:**\n\n*   `m == matrix.length`\n*   `n == matrix[i].length`\n*   `1 <= m, n <= 100`\n*   `-100 <= matrix[i][j] <= 100`\n*   `-105 <= k <= 105`\n\n**Follow up:** What if the number of rows is much larger than the number of columns?", "solution": "Step 1: Calculate the prefix sum of the matrix by iterating over each cell and adding the current value to the top, left, and top-left cells of the prefix sum matrix, while considering the matrix boundaries to avoid out-of-bounds errors.\nStep 2: Initialize two pointers, `left` and `right`, to the first column of the prefix sum matrix to represent the current window. Then, iterate over each row of the prefix sum matrix and for each row, calculate the maximum sum of the rectangle that can be formed with the current window by iterating over the `right` pointer and keeping track of the maximum sum found so far. After each iteration, move the `right` pointer to the right and update the `left` pointer if the sum of the rectangle exceeds `k`. Repeat this process until the `right` pointer reaches the last column of the prefix sum matrix.\nHere is the Python code that implements the given problem:\n\n```python\nclass Solution:\n    def maxSumSubmatrix(self, matrix, k):\n        m, n = len(matrix), len(matrix[0])\n        prefix_sum = [[0] * (n + 1) for _ in range(m + 1)]\n        \n        for i in range(1, m + 1):\n            for j in range(1, n + 1):\n                prefix_sum[i][j] = matrix[i - 1][j - 1] + prefix_sum[i - 1][j] + prefix_sum[i][j - 1] - prefix_sum[i - 1][j - 1]\n        \n        max_sum = float('-inf')\n        for left in range(n):\n            for right in range(left, n):\n                for i in range(1, m + 1):\n                    sum_ = prefix_sum[i][right + 1] - prefix_sum[i][left]\n                    if sum_ <= k and sum_ > max_sum:\n                        max_sum = sum_\n                    if prefix_sum[i][right + 1] - prefix_sum[i][left] > k:\n                        break\n        return max_sum\n```\n\nHere's an explanation of the code:\n\n*   We first create a prefix sum matrix `prefix_sum` of size `(m + 1) x (n + 1)` to store the prefix sums of the input matrix `matrix`.\n*   We then iterate over each cell in the input matrix and calculate the prefix sum for each cell by adding the current value to the top, left, and top-left cells of the prefix sum matrix. We use the formula `prefix_sum[i][j] = matrix[i - 1][j - 1] + prefix_sum[i - 1][j] + prefix_sum[i][j - 1] - prefix_sum[i - 1][j - 1]` to calculate the prefix sum for each cell.\n*   We initialize two pointers, `left` and `right`, to the first column of the prefix sum matrix to represent the current window.\n*   We then iterate over each row of the prefix sum matrix and for each row, we calculate the maximum sum of the rectangle that can be formed with the current window by iterating over the `right` pointer and keeping track of the maximum sum found so far. We use the formula `sum_ = prefix_sum[i][right + 1] - prefix_sum[i][left"}
```
由此便得到了最终的微调数据，可用于后续微调，在对应的文件夹中有我们开源的数据文件，可参考其中的格式和内容。

我们的灵感来源于[ReST-MCTS*](https://arxiv.org/abs/2406.03816)，原方法用于提升模型在数学领域的能力，使用蒙特卡洛便可得到最终结果；我们的方法用于提升模型在代码领域的推理能力，使用蒙特卡洛得到的是中间推理过程。
