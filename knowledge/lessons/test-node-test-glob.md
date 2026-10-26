# node --test 传目录参数会报 MODULE_NOT_FOUND
trigger: 在 Node v20+/v24 用 `node --test` 运行单元测试，且把测试目录（如 `frontend/test/`）作为参数传入时
scope: 全部（Node 测试运行器）

## 错误表现
```
$ node --test frontend/test/
Error: Cannot find module 'D:\...\frontend\test'
  code: 'MODULE_NOT_FOUND'
✖ fail 1
```
把目录当参数传给 `node --test` 时，Node 不递归扫描目录，而是把整个路径当模块去 require，导致 MODULE_NOT_FOUND。

## 正确做法
传 glob 模式（加引号防 shell 展开），或逐个文件：
```
node --test "frontend/test/*.test.js"
```
package.json 的 test 脚本同理：
```json
"scripts": { "test": "node --test \"frontend/test/*.test.js\"" }
```

## 原因
新版 `node --test` 的 CLI 参数是「测试文件路径或 glob」，不是「待扫描的目录」。裸目录既非文件也非 glob，被解析为模块标识去加载而失败。glob 形式让 Node 自行展开匹配测试文件。
