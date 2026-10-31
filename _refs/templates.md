# 状态文件模板（创建/改写对应文件前按需读取）

各状态文件的字段与模板。**只在要新建或改写某个文件时**读对应段，不必每次会话加载。
状态取值统一：`TODO | DOING | BLOCKED | REVIEW | DONE`。

## PROJECT.md（项目全局）  <a name="project"></a>
```markdown
# <项目名>
real_dir: <真实项目目录绝对路径>
desc: <一句话项目说明>

## 需求进度
| 需求ID | 标题 | 状态 | 分支 | 当前焦点 | 更新 |
|---|---|---|---|---|---|
| 001-xxx | ... | DONE | feature/xxx | - | 2026-10-20 |
| 002-xxx | ... | DOING | feature/xxx | 后端接口 | 2026-10-26 |
| 003-xxx | ... | TODO | - | - | - |

current_focus: 002-xxx
```

## STRUCTURE.md（真实目录结构缓存，见 AGENT_GUID §8）  <a name="structure"></a>
```markdown
# <项目名> 目录结构
real_dir: <真实项目目录绝对路径>
generated_at: 2026-10-30 15:00        # 上次扫描时间
commit: a1b2c3d                        # 上次扫描时的 HEAD，用于判过期
tree: |
  <真实目录的缩进树，只列有意义的目录/文件，忽略 .git/target/node_modules/缓存等>

## 关键路径速查
- 入口: backend/app.py
- 测试: backend/test_api.py（pytest）, frontend/test/*.test.js（node --test）
- 构建/运行: <命令>
- 约定: <包名、命名、分层等写代码必须遵守的约定>
```

## STATUS.md（需求状态 + 认领锁）  <a name="status"></a>
```markdown
# <需求ID> <标题>
goal: <这个需求要达成什么>
branch: feature/<需求ID>   # 从 main 开出，见 AGENT_GUID §6

## 角色状态（每个智能体只改自己那一行）
| 角色 | 状态 | owner | claimed_at | 当前步骤 | 下一步 |
|---|---|---|---|---|---|
| frontend | DOING | claude-code:frontend | 2026-10-26 14:00 | 登录表单 | 对接 /login |
| backend  | TODO  | - | - | - | 实现 /login |

blockers: <阻塞项，无则 ->
```

## CONTRACT.md（接口契约 / 握手）  <a name="contract"></a>
前后端并行开发的依据。谁改动接口谁追加一条「变更」，另一方确认后才能继续。
```markdown
# <需求ID> 接口契约
version: 3   # 每次结构变更 +1

## 接口
- POST /login  body:{user,pass}  resp:{token}   # 状态: 已确认

## 数据模型 / 组件约定
...

## 变更日志（追加）
- [v3][2026-10-26][codex:backend] /login resp 增加 refreshToken 字段 → 待 frontend 确认
```

## LOG.md（交接日志，只追加）  <a name="log"></a>
```markdown
- [2026-10-26 14:00][claude-code:frontend] 完成登录表单，等待 /login 契约确认
- [2026-10-26 14:20][codex:backend] /login 已实现，契约 v3 待前端确认
```
