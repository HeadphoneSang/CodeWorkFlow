# 001-file-transfer 接口契约
version: 2   # 每次结构变更 +1

## 技术栈约定
- 后端：FastAPI（Python），文件存本地 `uploads/` 目录，元数据存 `uploads/index.json`
- 前端：原生 HTML + JS（ES Modules），静态页由 FastAPI 挂载在 `/`
- 文件 id：uuid4 字符串

## 接口
- POST /upload   请求: multipart/form-data，字段名 `file`   resp 200: `{id, name, size}`   # 状态: 已确认
- GET  /files    请求: 无   resp 200: `{files: [{id, name, size, uploaded_at}]}`   # 状态: 已确认
- GET  /download/{id}   请求: 路径参数 id   resp 200: 文件流 + `Content-Disposition: attachment; filename="<name>"`；404: `{detail}`   # 状态: 已确认

## 数据模型 / 组件约定
- FileMeta: `{id: str, name: str, size: int, uploaded_at: str(ISO8601)}`
- 前端模块划分：
  - `frontend/src/api.js` — 纯 API 封装（upload/listFiles/downloadUrl），不碰 DOM，便于单测
  - `frontend/src/main.js` — DOM 接线，调用 api.js
  - `frontend/index.html` — 上传表单 + 文件列表容器
- 前端通过 `downloadUrl(id)` 生成 `/download/<id>` 链接，下载用 `<a href>` 触发

## 变更日志（追加）
- [v1][2026-10-26][claude-code:assistant] 契约初始化，待定义具体接口
- [v2][2026-10-26][claude-code:frontend] 定义 /upload、/files、/download/{id} 三接口与 FileMeta 模型；技术栈 FastAPI + 原生前端 → 待 backend 确认
- [v2][2026-10-26][zcode:backend] backend 确认契约 v2，三接口按原样实现，无结构变更（version 不变）
