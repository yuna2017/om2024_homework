# <center>AlphaFold3</center>

## 1.1 GPU版

### 1.1.1 模型部署
由于该程序所需显存为18G以上，故选择燕山大学HPC超算集群进行模型部署，各项依赖按照官方dockerfile文档安装，由于集群上CUDA驱动版本为12.0，与dockerfile文档的12.6不符，故需要重新选择jax版本，依据[jax安装文档](https://docs.jax.dev/en/latest/installation.html#nvidia-gpu)，使用如下命令安装jax：
```bash
pip install --upgrade pip
pip install --upgrade "jax[cuda12]"
```
运行命令后，jax版本为0.5.0，虽与dockerfile中要求版本不符，但仍可以正常运行。

### 1.1.2 硬件配置
使用超算集群compute01节点，该节点配置为 ：CPU：Intel Xeon Gold 5218R @ 2.10GHz，GPU：2* NVIDIA GeForce 3090 24G，运行内存：125G。

### 1.1.3 环境变量设置
```bash
export XLA_PYTHON_CLIENT_MEM_FRACTION=0.95
export JAX_TRACEBACK_FILTERING=off
```

### 1.1.4 程序运行命令
```bash
python ./run_alphafold.py \
  --input_dir=/input_dir \
  --output_dir=/output_dir \
  --model_dir=/model_dir \
  --norun_data_pipeline \
  --num_recycles=3 \
  --flash_attention_implementation=xla
```

### 1.1.5 程序运行结果
详见集群文件。

## 1.2 程序优化（注：程序优化部分由我提供思路，由AI完成代码）

### 1.2.1 优化思路概要
首先，通过分析程序代码并观察了程序运行结果，发现模型推理阶段的耗时占据程序总耗时的95%以上，故考虑优先优化模型推理阶段的时间。同时，发现在特征化阶段，也存在可优化的空间，故考虑同时优化特征化阶段的时间。

### 1.2.2 优化方法
模型推理阶段：使用jax编译缓存目录缓存已经编译的函数和模型参数，减少模型推理阶段的编译时间。
特征化阶段：定义FeatureCache类来将特征化数据写入缓存，通过缓存来降低重复计算，减少内存使用和计算时长，定义optimize_features函数来优化数据类型和内存布局，定义compress_features函数压缩特征化数据，减少内存使用，同时通过并行处理特征化数据来减少运行时长。

### 1.2.3 优化结果（注：优化后特征化阶段的提升需要先运行一遍程序，以生成缓存。）
优化前：总运行时长：3651.96秒，模型推理阶段：3353.36秒，特征化阶段：298.60秒。
优化后：总运行时长：3149.68秒，模型推理阶段：3042.80秒，特征化阶段：106.88秒。
总提升：13.7%，模型推理阶段提升：9.3%，特征化阶段提升：66.9%。

## 2.1 CPU版

### 2.1.1 模型部署
由于CPU版程序运行大输入时，内存占用达到100G以上，故选择燕山大学HPC超算集群的登录节点进行模型部署，模型部署与GPU版相同。

### 2.1.2 硬件配置
使用登录节点，该节点配置为：CPU：Intel Xeon Gold 5218R @ 2.10GHz，运行内存：125G。

### 2.1.3 环境变量设置
```bash
export MKL_DEBUG_CPU_TYPE=5
export MKL_ENABLE_INSTRUCTIONS=AVX2
export KMP_AFFINITY="granularity=fine,compact,1,0"
export MKL_DYNAMIC=FALSE
```
```python
import os
os.environ['JAX_PLATFORMS'] = 'cpu'      	
os.environ['JAX_SKIP_ROCM_TESTS'] = '1'   	
os.environ['JAX_SKIP_TPU_TESTS'] = '1'  		
os.environ['JAX_LOG_COMPILES'] = '0'  		
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'
```

### 2.1.4 程序运行命令
与GPU版相同。

### 2.1.5 程序运行结果
详见集群文件。

## 2.2 程序优化（注：程序优化部分由我提供思路，由AI完成代码）

### 2.2.1 优化思路概要
通过分析代码，发现内存与CPU通信，内存的使用存在优化空间，故考虑优化内存的使用，并发现程序运行时会出现NaN/Inf值，故考虑优化NaN/Inf值的处理。

### 2.2.2 优化方法
定义create_model_runner函数配置jax运行环境，包括禁用64位运算，设置线程数等；定义_post_process_result函数优化结果处理，进行数据类型的转化以提高效率；定义ModelRunner类，并定义_split_batch、run_inference、_merge_results三个函数，分别进行动态处理分割批处理数据、并行运行模型推理、并行合并计算结果；针对计算过程中的NaN/Inf值，定义NumericsHandler类，并定义handle_coordinate_numerics、handle_general_numerics、check_output_numerics三个函数，分别进行检测NaN/Inf值、将二者替换为合理数值、递归检查嵌套结构，以确保输出的有效性；定义CacheManager类，并定义_get_cache_key、_serialize_value、_deserialize_value、put函数，分别进行生成缓存键、序列化数据、反序列化数据、存储数据到缓存；定义MemoryManager类，并定义get_memory_usage、update、cleanup、monitor，分别进行获取当前进程内存使用情况、更新内存使用情况、清理内存、监控特定操作的内存变化。

### 2.2.3 优化结果
优化前：总运行时间：44102.1s
优化后：总运行时间：43929.5s
总体提升：0.4%，根据运行时CPU使用情况，可能与内存带宽有关。

## 3.1 程序运行过程
### 3.1.1 
首先在接收到json格式的输入后根据输入描述目标分子的组成及实验条件，然后根据运行命令传入的参数，设置循环次数，扩散样本数等。
### 3.1.2 
然后进行MSA，根据序列相似性及发布日期筛选结构模版，并加载CCD，RDKit，进行非标准残基的处理和小分子配体的3D构象生成，结束后，将这些编码成为多维向量。
### 3.1.3 
接下来进行模型推理，首先处理序列和配对特征，然后生成原子坐标，接下来进行循环优化，通过多次迭代来优化特征表示，每次迭代使用上次预测结果作为输入。
### 3.1.4 
然后，从模型输出的Frame Transforms解码原子坐标，并计算局部的键长，键角等然后将其正则化，接下来进行置信度评估，最后输出每个样本的3D结构为PDB文件等。

## 4 优化代码片段

### 4.1 编译缓存
```python
import os
import jax

def configure_compilation_cache(cache_dir: str):
    """配置JAX编译缓存目录"""
    os.makedirs(cache_dir, exist_ok=True)
    jax.config.update('jax_compilation_cache_dir', cache_dir)
    jax.config.update('jax_platforms', 'cpu,cuda')
    jax.config.update('jax_skip_backend_check', True)

class ModelCompiler:
    def __init__(self, model_config, device, model_dir):
        self._model_config = model_config
        self._device = device
        self._model_dir = model_dir
        
    @functools.cached_property
    def compiled_model(self):
        """缓存已编译的模型函数"""
        @hk.transform
        def forward_fn(batch):
            return model.Model(self._model_config)(batch)
            
        return jax.jit(forward_fn.apply, device=self._device)
```
### 4.2 特征缓存模块
```python
import hashlib
import numpy as np
import pathlib

class FeatureCache:
    def __init__(self, cache_dir: str):
        self.cache_dir = pathlib.Path(cache_dir)
        self.cache_dir.mkdir(parents=True, exist_ok=True)
        
    def _get_cache_key(self, fold_input) -> str:
        input_hash = hashlib.sha256(fold_input.to_json().encode()).hexdigest()
        return f"feat_{input_hash}"
        
    def load(self, fold_input):
        cache_path = self.cache_dir / f"{self._get_cache_key(fold_input)}.npz"
        return np.load(cache_path) if cache_path.exists() else None
        
    def save(self, fold_input, features):
        cache_path = self.cache_dir / f"{self._get_cache_key(fold_input)}.npz"
        np.savez_compressed(cache_path, **features)
```
### 4.3 特征优化模块
``` python 
import numpy as np
import scipy.sparse

def optimize_features(features, dtype=np.float32):
    """优化特征内存布局和数据类型"""
    optimized = {}
    for key, value in features.items():
        if isinstance(value, np.ndarray):
            # 统一数据类型
            if value.dtype in (np.float64, np.float32):
                value = value.astype(dtype)
            # 优化内存布局
            if not value.flags['C_CONTIGUOUS']:
                value = np.ascontiguousarray(value)
        optimized[key] = value
    return optimized

def compress_features(features, compression_level=2):
    """特征压缩优化"""
    compressed = {}
    for key, value in features.items():
        if isinstance(value, np.ndarray):
            # 稀疏矩阵压缩
            if compression_level >=2 and value.size > 1000:
                sparsity = np.count_nonzero(value)/value.size
                if sparsity < 0.1:
                    value = scipy.sparse.csr_matrix(value)
        compressed[key] = value
    return compressed
```
### 4.4 并行处理模块
``` python 

import multiprocessing
from typing import List

def parallel_featurize(inputs: List, num_workers: int = None):
    """并行特征化处理"""
    num_workers = num_workers or multiprocessing.cpu_count()
    
    def _process_single(args):
        # 实际特征化处理函数
        return featurisation.featurise_input_single(*args)
        
    with multiprocessing.Pool(num_workers) as pool:
        return pool.starmap(_process_single, inputs)
```