# 002-gateway-cors 网关统一处理跨域
goal: 让网关成为浏览器跨域的唯一权威——网关 CorsWebFilter（allowedOriginPatterns + allowCredentials），移除 note/auth 下游各自的 addCorsMappings，杜绝重复 Access-Control-Allow-Origin 头。
branch: feature/002-gateway-cors（基于 feature/auth 开出；测试全绿后已 merge 回 feature/auth，merge commit 9298360）

## 角色状态（每个智能体只改自己那一行）
| 角色 | 状态 | owner | claimed_at | 当前步骤 | 下一步 |
|---|---|---|---|---|---|
| backend | DONE | zcode:backend | 2026-10-30 21:54 | 实现完成，33 测试全绿，用户 Review 通过并随 feature/auth 合入 main | - |

blockers: ->

## 背景 / 关键决策（供 Review 参考）
1. 策略（用户确认）：网关单一权威 CORS——保留网关 CorsConfig、删除下游 note/auth 的 WebConfig.addCorsMappings（拦截器保留）。
2. 分支基线（用户纠正后确定）：**从 feature/auth 开分支**，测绿后合回 feature/auth（已执行，--no-ff 9298360）。最初误从 main 开分支，实测发现 **main 基线不可构建**：
   - note-module 在 main 上是 gitlink（mode 160000，指向 f23c068），reactor 组装直接失败；
   - dto-module 的 NoteDiffRequest import org.template.model.NoteBlock（反向依赖，feature/auth 已修）；
   - dto-module pom 把父工程 noteback 声明成 jar 依赖（单模块构建必炸，reactor 掩盖）。
   以上 main 问题均未修（不属本需求），恢复工作区时一并丢弃了在 main 上做过的临时修复。
3. feature/auth 上曾有两套半成品 CORS（未提交）：CorsConfig.java + application.yml 的 global-cors 块（属性名错，真名 globalcors，且 allowedOrigins:"*" 与 allowCredentials 组合非法）。**已被 002 的正确实现取代**：恢复 001 stash 时丢弃了这两件，仅保留 Main.java→NoteMainApplication 改名（现为 feature/auth 未提交改动）。
4. 测试：gateway CorsConfigTest 3 例（预检回显 origin 非 */实际请求带 ACAO+ACAC 续链/非跨域直通）；全 reactor 33 测试全绿（001 的 30 + 002 新增 3）。

## 遗留事项
- ~~main 不可构建问题（gitlink/反向 import/父 pom jar 依赖）待修复~~ → **已解决**：用户在 Review 后自行将 feature/auth 整体合入 main（207caaa），note-module 恢复正常 tree，main 全 reactor 33 测试全绿（22:37 复验）。
- feature/auth 与 feature/002-gateway-cors 分支已由用户删除。
