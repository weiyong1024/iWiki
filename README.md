# 欢迎来到iWiki

## 运行本地服务器
```
git clone git@github.com:weiyong1024/iWiki.git --depth=1

# 用新的 pyhton 虚拟环境运行
virtualenv pyenv -p python3
source pyenv/bin/active

cd iWiki

# 安装 mkdocs
pip3 install -U -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/

# 本地部署（两种方法）
# 1. 运行一个本地dev server，访问 http://127.0.0.1:8000 可以查看效果
mkdocs serve -v

# 2. 在 site 文件夹下生车静态页面
mkdocs build -v

# 获取 mkdocs 的命令行工具说明（了解命令行参数的含义）
mkdocs --help
# 也可以获取对单条命令的说明（如对build命令）
mkdocs build --help
```
