# 将 Streamlit 转换为可执行文件
#### [Tutorial](https://youtu.be/G7Qeg_rbYM8)

## 创建虚拟环境

```bash
pyenv virtualenv <version> .<env-name>
# 或者
python -m venv .<env-name>
# .点号很重要！
```

# 激活虚拟环境

```bash
pyenv activate <env-name>
# 或者
.<env-name>\\Scripts\\activate.bat
```

# 验证虚拟环境

```bash
python --version
```

# 停用虚拟环境

```bash
pyenv deactivate
# or
.<env-name>\\Scripts\\deactivate.bat
```

# 安装 Streamlit 和其他依赖库

```bash
# 你可以使用最新版本
pip install streamlit pyinstaller
```

# 添加主文件 (app.py)

```bash
echo > app.py
```

# 为可执行文件创建启动文件 (run_app.py)

```bash
echo > run_app.py
```

# 向文件中添加内容

- app.py:

```python
import streamlit as st

if __name__ == '__main__':
    st.header("Hello, World!")
```

- run_app.py

```python
from streamlit.web import cli

# 这个导入路径取决于你的 Streamlit 版本
if __name__ == '__main__':
    cli._main_run_clExplicit('app.py', args=['run'])
    # 我们将在我们的 Streamlit 框架中创建这个函数

```

# 导航到 Streamlit 路径

在我们使用的版本中，它位于: `.env\Lib\site-packages\streamlit\web\cli.py`

# 添加函数
```python
# ... def main(log_level="info"):
# [...]
# You can use any name you prefer as long as it starts with an underscore
def _main_run_clExplicit(file, is_hello, args=[], flag_options={}):
    bootstrap.run(file, is_hello, args, flag_options)

# ...if __name__ == "__main__":
# ...    main()
```

# 创建一个获取 Streamlit 元数据的钩子

- .\hooks\hook-streamlit.py
```python
from PyInstaller.utils.hooks import copy_metadata

datas = copy_metadata('streamlit')
```

# 编译应用程序
运行以下命令来创建第一个 run_app.spec 文件。
注意，如果你使用 auto-py-to-exe，你不能在这里编辑 spec 文件；你应该在高级选项的界面中编辑它们。

```bash
pyinstaller --onefile --additional-hooks-dir=./hooks run_app.py --clean
# --onefile: 创建一个输出文件
# --clean: 在构建之前删除缓存和临时文件
# --additional-hooks-dir: 一个用于搜索钩子的附加路径。此选项可以多次使用。
```

# 创建 Streamlit 配置文件

你可以将这些文件添加到项目的根目录和输出文件夹，或者只添加到输出文件夹。

- .streamlit\config.toml
```bash
[global]
developmentMode = false

[server]
port = 8502
```

# 将配置文件复制到输出文件夹
```bash
xcopy /s /e .streamlit output/.streamlit
# 选择 D = 目录
```

# 将 app.py 复制到输出文件夹
```bash
copy app.py output/app.py
```

# 将数据添加到 run_app.spec 中的新钩子
```python
...
a = Analysis(
    ...
    datas=[
        (".env/Lib/site-packages/altair/vegalite/v5/schema/vega-lite-schema.json",
        "./altair/vegalite/v5/schema/"),
        (".env/Lib/site-packages/streamlit/static",
        "./streamlit/static"),
        (".env/Lib/site-packages/streamlit/runtime",
        "./streamlit/runtime"),
    ]
    ...
)
...

```
# 注意事项
```python
# 
# 这个路径对应该以这种方式
# 但我相信这是因为我们将元组添加为模板
# (absolut_path, parent_path)
# 因此对于 `Lib/site-packages` 根目录中的文件
# 我们只需添加点作为父目录
# 即: (".envir/Lib/site-packages/wmi.py",".")
# 对于文件夹，行为相同
```

# 构建可执行文件

```bash
pyinstaller run_app.spec --clean
```

## 🎈  完成了！运行你的 run_app.exe 文件并见证魔法🪄

<pre>非常感谢: hmasdev<pre>
<pre>我正在组织来自 <a href="https://discuss.streamlit.io/t/using-pyinstaller-or-similar-to-create-an-executable/902/18"> Streamlit 论坛上 hmasdev 的解决方案</a></pre>
