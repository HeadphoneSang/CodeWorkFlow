# 001-split-auth 登录/注册从 note-module 拆分至 auth-module
goal: 将注册/登录/登出/验证码/邮箱登录/改密码迁入 auth-module（8081），note-module 改信任网关 X-User-Id 头（UserContext/ThreadLocal），网关新增 /api/auth/** 路由并统一 logout 黑名单 key
branch: feature/auth

## 角色状态（每个智能体只改自己那一行）
| 角色 | 状态 | owner | claimed_at | 当前步骤 | 下一步 |
|---|---|---|---|---|---|
| backend | REVIEW | zcode:backend | 2026-10-30 | 全链路验证通过（单测 30 绿 + E2E 7 步全通） | 等待用户 Code Review，通过后置 DONE |
| frontend | REVIEW | zcode:frontend | 2026-10-30 19:40 | 接口适配完成，等待用户 Code Review | 真机联调（待后端部署） |

## 已确认决策
1. 资料接口 /user/info、/user/update-info 留 note-module
2. 改密码 /user/password 归 auth-module
3. logout 黑名单 key bug 顺带修（统一为 note_backbond:logout:token）
4. devCapKey 验证码后门保留原样搬迁
5. note 改信任网关 X-User-Id 头 + UserContext(ThreadLocal)
6. 路由前缀 /api/auth/** → lb://auth；auth 端口 8081
7. auth 依赖 dto-module 共享 User/DTO/Result/RedisKey
8. 不加旧路径兼容转发，前端直接改

blockers: ->
