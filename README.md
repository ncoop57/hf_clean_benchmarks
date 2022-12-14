hf_clean_benchmarks
================

<!-- WARNING: THIS FILE WAS AUTOGENERATED! DO NOT EDIT! -->

This repository is heavily inspired by the [BigCode
repository](https://github.com/bigcode-project/bigcode-analysis/tree/main/data_analysis/decontamination)
and is mostly a refactoring of their code. Specifically, the main person
who worked on this repository is [Chenghao
Mou](https://github.com/ChenghaoMou) (Awesome work!).

## Install

``` sh
pip install hf_clean_benchmarks
```

## How to use

### Using the API

First you need to specify which benchmarks you want to clean your data
of. You can do this by creating dictionary with the benchmark name in
huggingface’s datasets repository as the key and the name of the column
containing the benchmark data as the value. For example, if you want to
clean your data of the `HumanEval` and `LAMBADA` benchmarks, you would
do the following:

``` python
# Benchmarks to clean
benchmarks = [
    {
        "name": "openai_humaneval",
        "splits": ["test"],
        "columns": ["prompt", "canonical_solution", "test"],
    },
    {
        "name": "lambada",
        "splits": ["test"],
        "columns": ["text"],
    },
]
```

You then pass this dictionary to the
[`BenchmarkCleaner`](https://ncoop57.github.io/hf_clean_benchmarks/core.html#benchmarkcleaner)
class. This class will download the benchmarks and construct the suffix
array for each benchmark. You can then use the `clean` method to clean a
huggingface dataset. For example:

``` python
from datasets import load_dataset
from hf_clean_benchmarks.core import BenchmarkCleaner

cleaner = BenchmarkCleaner(benchmarks, threshold=0.1, num_perm=128)

# load your dataset
dataset = load_dataset("bigcode/the-stack-smol", data_dir="data/python", split="train")

# clean the dataset
cleaned_dataset = cleaner.clean(dataset, column="content")
```

    Checking for false positives...: 100%|██████████| 8780/8780 [00:34<00:00, 251.05it/s]
    Checking for false positives...: 100%|██████████| 8805/8805 [07:34<00:00, 19.39it/s]

<pre style="white-space:pre;overflow-x:auto;line-height:normal;font-family:Menlo,'DejaVu Sans Mono',consolas,'Courier New',monospace"><span style="color: #7fbfbf; text-decoration-color: #7fbfbf">[11/06/22 10:34:43] </span><span style="color: #000080; text-decoration-color: #000080">INFO    </span> Data Number                   : <span style="color: #008080; text-decoration-color: #008080; font-weight: bold">10000</span>                                      <a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">core.py</span></a><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">:</span><a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py#210" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">210</span></a>
</pre>
<pre style="white-space:pre;overflow-x:auto;line-height:normal;font-family:Menlo,'DejaVu Sans Mono',consolas,'Courier New',monospace"><span style="color: #7fbfbf; text-decoration-color: #7fbfbf">                    </span><span style="color: #000080; text-decoration-color: #000080">INFO    </span> Duplicate Number              : <span style="color: #008080; text-decoration-color: #008080; font-weight: bold">4033</span>                                       <a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">core.py</span></a><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">:</span><a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py#211" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">211</span></a>
</pre>
<pre style="white-space:pre;overflow-x:auto;line-height:normal;font-family:Menlo,'DejaVu Sans Mono',consolas,'Courier New',monospace"><span style="color: #7fbfbf; text-decoration-color: #7fbfbf">                    </span><span style="color: #000080; text-decoration-color: #000080">INFO    </span> Duplicate Rate                : <span style="color: #008080; text-decoration-color: #008080; font-weight: bold">40.33</span>%                                     <a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">core.py</span></a><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">:</span><a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py#212" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">212</span></a>
</pre>
<pre style="white-space:pre;overflow-x:auto;line-height:normal;font-family:Menlo,'DejaVu Sans Mono',consolas,'Courier New',monospace"><span style="color: #7fbfbf; text-decoration-color: #7fbfbf">                    </span><span style="color: #000080; text-decoration-color: #000080">INFO    </span> Total Time                    : <span style="color: #008080; text-decoration-color: #008080; font-weight: bold">493.73</span> seconds                             <a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">core.py</span></a><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">:</span><a href="file:///home/nathan/projects/other/hf_clean_benchmarks/hf_clean_benchmarks/core.py#213" target="_blank"><span style="color: #7f7f7f; text-decoration-color: #7f7f7f">213</span></a>
</pre>

``` python
cleaned_dataset
```

    Dataset({
        features: ['content', 'avg_line_length', 'max_line_length', 'alphanum_fraction', 'licenses', 'repository_name', 'path', 'size', 'lang', '__id__'],
        num_rows: 5967
    })

### Using the CLI

First you need to specify which benchmarks you want to clean your data
of. You can do this by creating a json file with the benchmark name in
huggingface’s datasets repository as the key and the name of the column
containing the benchmark data as the value. For example, if you want to
clean your data of the `HumanEval` and `LAMBADA` benchmarks, you would
do the following:

file: `benchmarks.json`

``` json
[
    {
        "name": "openai_humaneval",
        "splits": ["test"],
        "columns": ["prompt", "canonical_solution", "test"],
    },
    {
        "name": "lambada",
        "splits": ["test"],
        "columns": ["text"],
    },
]
```

You then pass this json file to the
[`clean_dataset`](https://ncoop57.github.io/hf_clean_benchmarks/cli.html#clean_dataset)
command. This command will download the benchmarks and construct the
suffix array for each benchmark. You can then use the `clean` command to
clean a huggingface dataset. For example:

``` sh
clean_dataset \
    --dataset_name bigcode/the-stack-smol \
    --column_name content \
    --benchmark_configs_path benchmarks.json \
    --output_path /tmp/test.jsonl \
    --data_dir data/python \
    --dataset_split train \
    --save_json
```
