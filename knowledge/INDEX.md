# 经验索引（会话启动只加载本文件，正文按需读取）

格式：`- [触发条件] 一句话结论 → lessons/文件`

- [在本机执行任何 shell 命令前] 本机是 Windows，命令按 Unix/bash 语法写，勿用 NUL、反斜杠路径等 cmd 写法 → lessons/env-windows-shell.md
- [用 node --test 跑单测且参数传目录时] 裸目录会被当模块 require 报 MODULE_NOT_FOUND，改传 glob 模式 "dir/*.test.js" → lessons/test-node-test-glob.md
- [用户报"前端请求失败"但后端 curl 正常时] 先查地址栏是否 file:// 双击打开——ES module 被 CORS 拦截、fetch 相对路径失效，让用户改走后端挂载地址 → lessons/env-file-protocol-fetch.md
- [在 note 项目 dto-module 新建 Java 文件或报"程序包X不存在"] 包名是 com.chcbcraft（比 groupId 多个 c），别写成 com.chbcraft → lessons/code-package-typo-chcbcraft.md
- [本机要跑 mvn 或报 compiler-plugin 需 Maven≥3.6.3] 用 D:/tools/apache-maven-3.9.9/bin/mvn.cmd，PATH 里没有 mvn → lessons/env-maven-3-6-2-too-old.md
- [git-bash 里 curl -d 发中文 JSON] 请求体被按 GBK 编码，服务端报 Invalid UTF-8 middle byte，用 ASCII 或 @file.json → lessons/env-curl-gbk-json.md
- 测试冲突
