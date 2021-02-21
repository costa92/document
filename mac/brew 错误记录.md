# brew 错误记录


### 1.若出现 Error: Checksum mismatch.

报错代码如下：

```
curl: (56) LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54
Error: Checksum mismatch.
Expected: b065e5e3783954f3e65d8d3a6377ca51649bfcfa21b356b0dd70490f74c6bd86
Actual: e8a348fe5d5c2b966bab84052062f0317944122dea5fdfdc84ac6d0bd513c137
Archive: /Users/joyce/Library/Caches/Homebrew/portable-ruby-2.6.3_2.yosemite.bottle.tar.gz
To retry an incomplete download, remove the file above.
Error: Failed to install Homebrew Portable Ruby (and your system version is too old)!
Failed during: /usr/local/bin/brew update --force

```

这里是由Homebrew目录下的portable-ruby-2.6.3_2.yosemite.bottle.tar.gz文件引起的安装中断，只需要到上面对应的路径里，删掉这个文件，重新执行安装命令即可：

```
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install)"

```

### 2.若卡在了Cloning into...

![](https://img2020.cnblogs.com/blog/2107879/202007/2107879-20200725185753122-2131429509.png)
由这里的龟速可断定卡住了，立马用Control + C中断脚本，然后执行以下命令：

```
cd "$(brew --repo)/Library/Taps/"
mkdir homebrew && cd homebrew
git clone git://mirrors.ustc.edu.cn/homebrew-core.git

```

执行后可看到：

![](https://img2020.cnblogs.com/blog/2107879/202007/2107879-20200725192721482-1264798400.png)
速度立马快得飞起，一下子就能装好。

注：最后出现 Installation successful! 或者 Checking out files: 100% (5392/5392), done. 说明安装成功。

## Homebrew安装完为何需要配置

前面已经提到，Homebrew通常用来下载软件的，但它在安装软件时非常慢。为了提升安装速度，需要更改 Homebrew 的安装源，将其替换成国内镜像。

这里用的是由中科大负责托管维护的 Homebrew 镜像。其中，前两个为必须配置的项目，后两个可按需配置。

### 1.必备设置

*   替换 brew.git：

```
git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git

```

*   替换 homebrew-core.git：

```
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

```

### 2.按需设置

*   替换 homebrew-cask.git：

```
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

```

*   替换homebrew-bottles：

首先要先区分你的mac用哪种终端工具，如果是 bash，则执行：

```
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

```

若是 zsh，则执行：

```
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc

```

注：Homebrew 主要由四个部分组成: brew、homebrew-core 、homebrew-cask、homebrew-bottles，它们对应的功能如下：

| 组成 | 功能 |
| --- | --- |
| Homebrew | 源代码仓库 |
| homebrew-core | Homebrew 核心源 |
| homebrew-cask | 提供macos应用和大型二进制文件的安装 |
| homebrew-bottles | 预编译二进制软件包 |

## Homebrew 基本用法有哪些

```
// 查询：
brew search 软件名

// 安装：
brew install 软件名

// 卸载：
brew uninstall 软件名

// 更新 Homebrew：
brew update 

// 查看 Homebrew 配置信息：
brew config 

```

注：使用官方脚本同样会遇到uninstall地址无法访问问题，可以替换为下面脚本：

```
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/uninstall)"

```

