# Windows 机器上按 Linux 习惯执行命令导致失败
trigger: 需要在当前工作区执行 shell 命令（重定向、删临时文件、路径拼接）时
scope: windows / 全部项目

## 错误表现
- 用 `> NUL`（cmd 写法）代替 `/dev/null`，在非 cmd shell 下报错。
- 路径写 `D:\pro\...` 反斜杠形式，bash 下转义出错。

## 正确做法
- 丢弃输出用 `/dev/null`，不用 `NUL`。
- 路径一律用正斜杠：`D:/pro/...`。

## 原因
本机为 Windows 11，但工作 shell 是 bash（Unix 语义），cmd 专有写法不可用。
