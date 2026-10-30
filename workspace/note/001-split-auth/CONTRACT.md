# 001-split-auth 接口契约
version: 1

## 路由拓扑（本次变更后）
| 网关路径 | 目标服务 | 说明 |
|---|---|---|
| /api/auth/** | auth (8081) | 认证域：注册/登录/登出/验证码/邮箱登录/改密码/token-info |
| /api/note/** | note (8080) | 笔记域 + 资料接口 /user/info、/user/update-info |

网关 RewritePath 剥掉第一段前缀（/api/auth/user/login → auth 服务的 /user/login）。

## auth 服务接口清单（前端需同步改地址）
| 接口 | 旧地址 | 新地址 |
|---|---|---|
| 注册 | /api/note/user/register | /api/auth/user/register |
| 登录 | /api/note/user/login | /api/auth/user/login |
| 邮箱验证码登录 | /api/note/user/email-login | /api/auth/user/email-login |
| 发送邮箱验证码 | /api/note/user/send-email-code | /api/auth/user/send-email-code |
| 图形验证码 | /api/note/captcha | /api/auth/captcha |
| 登出 | /api/note/user/logout | /api/auth/user/logout |
| token 换用户信息 | /api/note/user/token-info | /api/auth/user/token-info |
| 修改密码 | /api/note/user/password | /api/auth/user/password |

不变的接口（仍在 note）：GET /api/note/user/info、PUT /api/note/user/update-info、全部笔记接口。

## 网关白名单（免鉴权路径）
/api/auth/user/login、/api/auth/user/register、/api/auth/user/send-email-code、
/api/auth/user/email-login、/api/auth/user/token-info、/api/auth/captcha

## 内部约定（前端无感）
- 网关校验 JWT 后注入 X-User-Id / X-User-Account 头并删除客户端伪造的同名头；下游服务经 UserContext(ThreadLocal) 读取。
- logout 黑名单 key 统一为 note_backbond:logout:token（修复原先写入 key 与检查 key 不一致导致登出不生效的 bug）。
- JWT secret/expiration 从 Nacos jwt.properties(GATEWAY_GROUP) 读取，默认值兼容存量 token。

## 变更日志（追加）
- [v1][2026-10-30][zcode:backend] 初版：拆分范围与路由契约 → 待用户确认
