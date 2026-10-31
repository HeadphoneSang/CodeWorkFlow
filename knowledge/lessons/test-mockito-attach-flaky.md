# Mockito/byte-buddy attach 偶发失败——先原样重跑一次再排查
trigger: mvn test（尤其全 reactor）偶发 UserServiceTest 等大批 ERROR，报 Could not initialize plugin MockMaker / Could not self-attach using external process
scope: windows / note 项目及一切用 mockito inline mock maker 的 Maven 项目

## 错误表现
```
java.lang.IllegalStateException: Could not initialize plugin: interface org.mockito.plugins.MockMaker
Caused by: Could not initialize inline Byte Buddy mock maker.
Caused by: Could not self-attach to current VM using external process
```
同一分支同一命令：一次全红（10 ERROR，1.8s 即炸）、重跑全绿（8s+ 正常耗时）。故障时 auth-module 下可能残留 hs_err_pid*.log / replay_pid*.log。

## 正确做法
1. **先原样重跑一次**。attach 失败是外部进程 attach 的竞态（Windows 常见），重跑即绿基本可判偶发。
2. 判别回归：在改动基线分支跑同样命令对照（基线也偶发红 = 环境问题；基线稳定绿 + 改动分支稳定红 = 回归）。
3. 排查时用模块组合二分（-pl A,B -am），能快速证明「模块组合/顺序无关」。
4. `-DargLine=...` 不一定传入 surefire fork JVM，别用它做结论依据。
5. 事后删掉 hs_err_pid*.log / replay_pid*.log（untracked 垃圾）。

## 原因
mockito-inline 的 ByteBuddyMockMaker 需要 self-attach agent；self-attach 默认禁用，byte-buddy 起外部进程 attach，该机制在 Windows 上有偶发竞态（杀软/时序）。偶发 ≠ 代码回归。
