# 002-gateway-cors 交接日志（只追加）

- [2026-10-30 21:54][opencode:backend] 需求占位登记：PROJECT.md 追加 002 行、建分区与 STATUS.md。策略经用户确认=「网关单一权威 CORS」，分支=从 main 开 feature/002-gateway-cors。已查明：main 上网关无任何 CORS；feature/auth 上存在两套半成品 CORS（CorsConfig.java 正确 + yml global-cors 块属性名错且组合非法）+ 下游 WebConfig CORS，三者并存有重复 ACAO 头风险。
- [2026-10-30 22:0x][zcode:backend] 用户纠正分支基线：改从 feature/auth 开 feature/002-gateway-cors（最初误从 main 开）。废弃 main 基线上的临时修复（dto pom noteback 依赖、NoteDiffRequest import），main 不可构建三因素记录在 STATUS。
- [2026-10-30 22:1x][zcode:backend] 实现 3 commit：① 网关 CorsWebFilter（cherry-pick 自旧分支 1c2b85e）② 删 note/auth 下游 addCorsMappings（9e8c81e）③ gateway CorsConfigTest 3 例（740c14b）。
- [2026-10-30 22:2x][zcode:backend] 测试排查记录：全 reactor 曾 2 次 UserServiceTest 10 ERROR（byte-buddy MockMaker external attach 失败）。对照实验（基线/002 × 全 reactor/-pl 组合）证明与 002 无关且可复绿，终审 002 全 reactor 33 测试全绿 BUILD SUCCESS——判定为 attach 偶发竞态，已沉淀 knowledge。附带发现：单模块 -pl 构建需本地仓库 jar，之前失败 install 留下 dto-module 半截元数据（pom 有 jar 无）会污染单模块构建，用 -am 全 reactor 方式规避。
- [2026-10-30 22:3x][zcode:backend] 按用户指示 merge feature/002-gateway-cors → feature/auth（--no-ff，9298360）。恢复 001 stash：保留 Main.java→NoteMainApplication 改名（未提交，归 001），丢弃其中的 CorsConfig.java 半成品与 yml global-cors 坏块（被 002 取代）；stash 已 drop，JVM 崩溃日志已清。置 REVIEW，等待用户 Code Review。
- [2026-10-30 22:4x][zcode:backend] 沉淀 knowledge：test-mockito-attach-flaky（attach 偶发竞态先重跑）。STRUCTURE.md 刷新至 9298360（gateway 新增 test 目录；补「CORS 只在网关」「main 基线不可构建」「-pl 须带 -am」三条约定）。
- [2026-10-30 22:37][zcode:backend] 用户 Code Review 通过，002 置 DONE。改动随 feature/auth 由用户合入 main（207caaa）；main 全 reactor 33 测试全绿（22:37 复验），CORS 单一权威生效于 main。
