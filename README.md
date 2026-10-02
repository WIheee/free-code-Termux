需要用到 bun-termux-loader

有什么不懂的话请问 AI

哦对了需要在电脑上构建不能在手机上构建
原因是手机上编译会依赖解析失败和内存不足
而且编译产物需要额外工具包装才能在 Termux 上跑

我这个仓库已经适配好了 直接构建就行 不用改代码

## 如何构建

1 先克隆我的仓库

```bash
git clone https://github.com/WIheee/free-code-Termux.git
cd free-code-Termux
```

2 运行 `bun install` 不过请确保你的电脑上有 bun
没有的话先装一个

```bash
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```

3 直接构建

```bash
bun scripts/build.ts --compile --dev --feature-set=dev-full
```

产物在 `dist/cli-dev` 是 ARM64 架构

4 用 bun-termux-loader 包装一层

```bash
git clone https://github.com/Hope2333/bun-termux-loader.git
cd bun-termux-loader
make
python3 build.py ~/free-code-Termux/dist/cli-dev ~/free-code-termux
```

生成的 `~/free-code-termux` 才是最终能跑的二进制

## 如何使用构建好的二进制文件

如果不想自己构建 也可以直接用我发布的发行版

安装 Termux 并把二进制文件
或者你自己构建的二进制文件
放到 Termux 的 `~/` 目录下

安装环境用这一条

```bash
pkg update
pkg install glibc-runner ripgrep -y
```

给予执行权限 确保它在 `~/` 下

```bash
chmod +x ~/free-code-termux
```

然后测试一下能不能打开

```bash
~/free-code-termux
```

如果弹出界面的话那就说明你成功了

然后就是配置问题了
放心我已经帮你搞了大部分的内容了
联网搜索用的是 Deepseek 的
接下来只需要你自己填这些环境变量
把它们填进 Termux 的 `~/.bashrc` 下

```
# free-code 配置
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
export ANTHROPIC_AUTH_TOKEN="sk-你的DeepSeek密钥"
export ANTHROPIC_MODEL="deepseek-flash"
export TMPDIR=$PREFIX/tmp
export CLAUDE_CODE_TMPDIR=$HOME/tmp
export USE_BUILTIN_RIPGREP=0
mkdir -p $TMPDIR $HOME/tmp
```

填完记得 `source ~/.bashrc` 让它生效

## 常见问题

报 `ld.so failed`

```bash
pkg install glibc-runner -y
unset LD_PRELOAD
```

报 `Permission denied`

```bash
chmod +x ~/free-code-termux
```

报 `EACCES mkdir /tmp/claude-xxx`
检查 `~/.bashrc` 里的 `CLAUDE_CODE_TMPDIR` 和 `TMPDIR` 有没有设对

搜索时报找不到 ripgrep

```bash
pkg install ripgrep -y
export USE_BUILTIN_RIPGREP=0
```

API 报 401 或 403

```bash
echo $ANTHROPIC_AUTH_TOKEN
```

应该显示 `sk-` 开头的密钥 没有就重新配