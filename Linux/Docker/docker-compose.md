### ARM64

ARM64 只能使用 pip 安装：

```bash
pip install docker-compose
```

如果出现 cffi 相关错误：

```bash
apt-get install python-cffi
```

`fatal error: ffi.h: No such file or directory`

```bash
apt-get install libffi-dev
```

如果出现 `fatal error: Python.h: No such file or directory`，需要安装 **python-dev** 包。

python3.x

```bash
apt-get install python3.x-dev
```

python2.7

```bash
apt-get install python-dev
```