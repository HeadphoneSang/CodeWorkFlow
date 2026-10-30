# 本机 mvn 不在 PATH，wrapper 里只有 Maven 3.6.2（Spring Boot 3.3 编译不过）
trigger: 在本机要跑 mvn 构建/测试，或 mvn 报「maven-compiler-plugin requires Maven version 3.6.3」时
scope: windows / 全部项目

## 错误表现
- `mvn` 命令不存在（PATH 里没有 Maven，~/.m2/wrapper 里只有 3.6.2）。
- 用 3.6.2 构建 Spring Boot 3.3.4 项目报：`maven-compiler-plugin:3.13.0 requires Maven version 3.6.3`。

## 正确做法
- 直接用已装好的 Maven 3.9.9：`D:/tools/apache-maven-3.9.9/bin/mvn.cmd`（bash 下全路径调用）。
- 本地仓库在默认 `C:/Users/headp/.m2/repository`，settings.xml 已配镜像，无需额外配置。
- Windows 解压 zip：git-bash 的 GNU tar 不认 zip，用 `powershell -NoProfile -Command "Expand-Archive ..."`。

## 原因
Spring Boot 3.3 父 pom 带 maven-compiler-plugin 3.13.0，该插件要求 Maven ≥3.6.3。
