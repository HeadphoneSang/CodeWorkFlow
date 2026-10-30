# git-bash 下 curl 发中文 JSON 请求体被按 GBK 编码，服务端报 JSON parse error
trigger: 在 git-bash 里用 curl -d 发含中文的 JSON 给接口做调试/验证时
scope: windows / 全部项目

## 错误表现
- 服务端日志：`HttpMessageNotReadableException: JSON parse error: Invalid UTF-8 middle byte 0xe9`，接口返回 Spring 默认 400。
- bash 里看 JSON 明明合法。

## 正确做法
- 调试用请求体避免中文（nickname 等用 ASCII），或把 JSON 写入 UTF-8 文件后 `curl -d @file.json`。
- 真要带中文：`curl --data-binary @<(printf '...')` 也可能受影响，最稳是文件方式。

## 原因
Windows git-bash 的 curl 把命令行参数按系统 ANSI 代码页（GBK）编码发出，Spring 按 UTF-8 解析失败。业务代码无辜。
