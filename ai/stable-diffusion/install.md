
环境准备

安装 python 虚拟环境

### 安装  Anaconda

1. 安装软件依赖包
```sh
apt install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6
```
2.  下载Anaconda安装包

```sh
wget https://repo.anaconda.com/archive/Anaconda3-2022.10-Linux-x86_64.sh
```
3. 安装
```sh
 bash anaconda.sh
```
安装过程基本上一路回车就可以了。

3. 修改 .bashrc

现在,您可以通过修改 `~/.bashrc`文件来激活安装。

在 ~/.bashrc 末尾添加:

```bash
export PATH="~/anaconda3/bin":$PATH
source ~/anaconda3/bin/activate
```

4. 安装是否成功

```sh
conda list
```
 5.  通过Anaconda设置Python环境
```sh
conda create --name diffusion python=3.10.6
```
6. 进入新环境
```sh
conda activate diffusion
```

如果执行失败:
```sh
CommandNotFoundError: Your shell has not been properly configured to use 'conda activate'.
To initialize your shell, run

    $ conda init <SHELL_NAME>

Currently supported shells are:
  - bash
  - fish
  - tcsh
  - xonsh
  - zsh
  - powershell

See 'conda init --help' for more information and options.

IMPORTANT: You may need to close and restart your shell after running 'conda init'.

```

如果是使用 fish
```sh 
conda init fish 
```

重新启动 shell

```sh
fish
```
再重新执行此 `conda activate diffusion` 

7. 退出
```sh
conda deactivate
```

要关闭自动激活 base 环境
```sh
 conda config --set auto_activate_base false
```
### 安装 stable-diffusion-webui
  
  克隆代码
```sh
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```

安装扩展

```sh
cd  stable-diffusion-webui && pip3 install -r requirements.txt
```

stable-diffusion-webui根目录中运行

```sh
./webui.sh
```


```sh
./webui.sh --skip-torch-cuda-test --use-cpu all --precision full --no-half
```

安装 cuda12.5版本
### 升级 cuda 到 12.5版本

首先打开 nvdia 官网 [[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)] 下载，我的是 ubuntu 24.04 但还没有对应的版本，我就选择了 22.04 一样安装成功了。  

安装步骤：
```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-ubuntu2404.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.5.0/local_installers/cuda-repo-ubuntu2204-12-5-local_12.5.0-555.42.02-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-5-local_12.5.0-555.42.02-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-5-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-5
sudo apt-get install -y cuda-drivers

```

安装 的汉化扩展
1.在任意目录下使用`git clone https://github.com/VinsonLaro/stable-diffusion-webui-chinese`

2.进入下载好的文件夹,把"localizations"文件夹内的"Chinese-All.json"和"Chinese-English.json"复制到"stable-diffusion-webui\localizations"目录下

3.点击"Settings"，左侧点击"User interface"界面，在界面里最下方的"Localization (requires restart)"，选择"Chinese-All"或者"Chinese-English"

4.点击界面最上方的黄色按钮"Apply settings"，再点击右侧的"Reload UI"即可完成汉化


下载需要的模型文件

[https://civitai.com/](https://sspai.com/link?target=https%3A%2F%2Fcivitai.com%2F)
https://www.liblib.art/



参考:
[Python conda](https://yfi.moe/post/pyenv-conda-together)
[# Ubuntu 22.04上安装Anaconda，及 conda 的基础使用](https://blog.csdn.net/JineD/article/details/129507719)
[Stable-Diffusion的WebUI部署](https://blog.csdn.net/maiya_yayaya/article/details/139519725?spm=1001.2101.3001.6650.9&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-9-139519725-blog-135525227.235%5Ev43%5Epc_blog_bottom_relevance_base9&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-9-139519725-blog-135525227.235%5Ev43%5Epc_blog_bottom_relevance_base9)
[# AI绘画(Stable Diffusion)喂饭级教程-第2篇(SD大模型详解)](https://blog.csdn.net/WANGJUNAIJIAO/article/details/140264159?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-140264159-blog-141104982.235^v43^pc_blog_bottom_relevance_base9&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

