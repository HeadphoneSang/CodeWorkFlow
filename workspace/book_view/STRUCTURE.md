# book_view 目录结构
real_dir: D:\pro\book_view
generated_at: 2026-10-30 16:00
commit: 52c35d9

tree: |
  book_view/
  ├── package.json               # 前端脚本：npm test → node --test "frontend/test/*.test.js"
  ├── requirements.txt           # fastapi / uvicorn / python-multipart / httpx / pytest
  ├── backend/                   # Python FastAPI 后端
  │   ├── app.py                 # 入口：FastAPI app，挂载前端静态目录 + 上传/下载路由
  │   ├── store.py               # 文件索引读写（uploads/index.json）
  │   └── test_api.py            # pytest 接口测试
  ├── frontend/                  # 原生 ES module 前端（无构建步骤）
  │   ├── index.html
  │   ├── style.css
  │   ├── src/{api.js,main.js}   # api.js 封装 fetch，main.js 处理 DOM
  │   └── test/api.test.js       # node:test 单测
  └── uploads/                   # 运行时数据：uuid 命名的文件 + index.json 清单

## 关键路径速查
- 后端入口: `backend/app.py`（`uvicorn app:app` 起服务，同时挂载 frontend 静态文件）
- 后端测试: `python -m pytest backend/test_api.py`（在仓库根执行）
- 前端测试: `npm test`（脚本已写成 glob，勿改成裸目录，见 knowledge）
- 前端无打包器：直接改 `frontend/src/*.js`，浏览器通过后端挂载地址访问
- **必须走后端挂载地址打开前端**，`file://` 双击会让 ES module 被 CORS 拦截、fetch 相对路径失效（见 knowledge）
- 存储: 无数据库，文件落 `uploads/`，元数据在 `uploads/index.json`（数组：id/name/size/uploaded_at）

## 约定
- 后端路由与前端 `frontend/src/api.js` 一一对应，改接口两边同步改并更新 CONTRACT.md。
- 前端保持零依赖、零构建；不引入打包器除非用户明确要求。
