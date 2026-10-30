# dto-module 包名是 com.chcbcraft（历史 typo），新文件极易写错
trigger: 在 note 项目 dto-module 下新建 Java 文件、或编译报「程序包X不存在/找不到符号」但类明明存在时
scope: note 项目（D:/pro/note_project/note）

## 错误表现
- 新写的类 package 声明成了 `com.chbcraft.dto.enums`（正确是 `com.chcbcraft`，多一个 c）。
- javac 不报编译错误（maven 按文件列表编译，允许 package 与目录不一致），但 class 产物落到 `com/chbcraft/` 目录，其他类 import `com.chcbcraft.dto.enums.X` 时报「程序包X不存在」。
- 排查时极易被误导：bash 的 find/ls 能看到文件、产物也在，javap/测试却找不到。

## 正确做法
- dto-module 内所有新文件的 package 一律写 `com.chcbcraft.dto...`（与 groupId com.chbcraft 拼写不同！）。
- 遇到「类存在但 import 不到」：第一步先核对 package 声明与物理目录是否逐字符一致（用 `sed -n 1p` 看 package 行），不要先怀疑 classpath/构建工具。

## 原因
groupId 是 `com.chbcraft`，但 dto-module 包名历史上写成了 `com.chcbcraft`（c-h-c-b），两者只差一个字母 c，肌肉记忆会跟着 groupId 写。
