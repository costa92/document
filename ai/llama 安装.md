## 安装环境

```Bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.11 python3.11-venv -y

```

安装虚拟环境

```Bash
/usr/bin/python3.11 -m venv venv
```

使用虚拟

```Bash
source venv/bin/activate
# 如果使用的 fish
source venv/bin/activate.fish

```

安装 Install PyTorch:

```Bash
 pip install --pre torch torchvision --extra-index-url https://download.pytorch.org/whl/nightly/cpu
```

## 下载

1. 下载 llama.cp

```Bash
git clone https://github.com/ggerganov/llama.cpp.git 
```

1. 下载通义千问1.5-7B模型 或 1.5-32B 模型

```Bash
# 使用 huggingface 仓库
git clone https://huggingface.co/Qwen/Qwen1.5-7B-Chat
# 或
git clone https://huggingface.co/qwen/Qwen1.5-32B-Chat.git


# 使用 modelscope 仓库
git clone https://www.modelscope.cn/Qwen/Qwen1.5-7B-Chat
# 或
git clone https://www.modelscope.cn/qwen/Qwen1.5-32B-Chat.git
```

## 编译 llama.cp

```Bash
cd llama.cpp 
make
```

### 安装llama 依赖

```Bash
python3 -m  pip install -r requirements.txt
```

### 转换 Qwen 模型为 GGUF

什么是 GGUF? GGUF是一种用于存储用于GGML推断和基于GGML的执行器的模型的文件格式。GGUF是一种二进制格式，旨在快速加载和保存模型，并易于阅读。传统上，模型是使用PyTorch或其他框架开发的，然后转换为GGUF以在GGML中使用。

GGUF是GGML、GGMF和GGJT的后继文件格式，旨在通过包含加载模型所需的所有信息来消除歧义。它还设计为可扩展的，因此可以向模型添加新信息而不会破坏兼容性，更多信息访问[官方说明文档](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md)。

查看文件结构

```Bash
$ tree -L 1
.
├── llama.cpp  
├── Qwen1.5-32B-Chat
└── venv
```

执行这转换命令

```Bash
python3 convert-hf-to-gguf.py  ../Qwen1.5-32B-Chat

=> .....
INFO:hf-to-gguf:Model successfully exported to '../Qwen1.5-32B-Chat/ggml-model-f16.gguf'

```

最后的结果表示已经转为 F16 的 gguf 格式的模型了

### 量化模型

将gguf 的模型量化到INT4

```Bash
./llama-quantize  ../Qwen1.5-32B-Chat/ggml-model-f16.gguf  ./models/qwen1.5-chat-ggml-model-Q4_K_M.gguf Q4_K_M
=>  ....
[ 767/ 771]                   blk.63.attn_q.bias - [ 5120,     1,     1,     1], type =    f32, size =    0.020 MB
[ 768/ 771]                 blk.63.attn_q.weight - [ 5120,  5120,     1,     1], type =    f16, converting to q4_K .. size =    50.00 MiB ->    14.06 MiB
[ 769/ 771]                   blk.63.attn_v.bias - [ 1024,     1,     1,     1], type =    f32, size =    0.004 MB
[ 770/ 771]                 blk.63.attn_v.weight - [ 5120,  1024,     1,     1], type =    f16, converting to q6_K .. size =    10.00 MiB ->     4.10 MiB
[ 771/ 771]                   output_norm.weight - [ 5120,     1,     1,     1], type =    f32, size =    0.020 MB
llama_model_quantize_internal: model size  = 62014.27 MB
llama_model_quantize_internal: quant size  = 18780.70 MB

main: quantize time = 421046.36 ms
main:    total time = 421046.36 ms



```

../Qwen1.5-32B-Chat/ggml-model-f16.gguf 是转换的源文件路径

./models/qwen1.5-chat-ggml-model-Q4_K_M.gguf 是转换成功生成的文件路径

Q4_K_M 是量化方法

新版量化方法包括：

- Q2_K
- Q3_K_S, Q3_K_M, Q3_K_L
- Q4_K_S, Q4_K_M
- Q5_K_S, Q5_K_M
- Q6_K

### 运行

### 运行测试

```Bash
./llama-cli -m ./models/qwen1.5-chat-ggml-model-Q4_K_M.gguf -n 128
=> 

1) 甲乙丙丁四地中，最可能出现水土流失的是
A. 甲
B0 乙
C0 丙
D0 丁
答案：C
关键点：中国分区地理，水土流失及治理，区域农业生产的条件、布局特点、问题


llama_print_timings:        load time =   17027.80 ms
llama_print_timings:      sample time =      22.98 ms /   128 runs   (    0.18 ms per token,  5571.27 tokens per second)
llama_print_timings: prompt eval time =       0.00 ms /     0 tokens (    -nan ms per token,     -nan tokens per second)
llama_print_timings:        eval time =  867086.12 ms /   128 runs   ( 6774.11 ms per token,     0.15 tokens per second)
llama_print_timings:       total time =  867381.32 ms /   128 tokens
Log end
```

注意： 这个运行等待的时间比较长

接下来我们进入对话模型。如何启动呢？我们这里查看 examples/chat.sh的启动方式，来编写启动 Qwen 模型命令。

```Bash
./llama-cli -m ./models/qwen1.5-chat-ggml-model-Q4_K_M.gguf  -n 256 --grammar-file grammars/json.gbnf -p 'Request: schedule a call at 8pm; Command:'

```

## 错误

1. 通义千问safetensors_rust.SafetensorError: Error while deserializing header: HeaderTooLarge解决方法

安装 gi-lfs

```Bash
sudo apt-get install git-lfs

```

进入 Qwen1.5-32B-Chat 执行 git lfs pull

```Bash
cd Qwen1.5-32B-Chat && git lfs pull
```