# STRUCTURE.md 扫描/刷新流程（刷新结构缓存时按需读取）

只在需要**生成或刷新** `./workspace/<项目>/STRUCTURE.md` 时读这里（判过期、读取时机见 AGENT_GUID §8）。模板见 `_refs/templates.md#structure`。

## 刷新步骤
1. 在真实目录扫描：忽略 `.git`、`target`、`node_modules`、`__pycache__`、`.pytest_cache`、`.idea`、`.venv`、`dist`、`build` 等噪音目录。
2. 记录 `generated_at`（当前时间）与 `commit`（当前 HEAD）。
3. `tree:` 段只列**对写代码有意义**的层级（模块根、源码包、测试目录、配置文件），深度以能定位文件为准，不追求穷举。
4. 「关键路径速查」段写清：入口文件、测试文件与测试命令、构建/运行命令、写代码必须遵守的约定（如 Java 包名、前端模块规范）。这些是下次实现前真正要读的信息，比树本身更有价值。
5. 整体控制在 ~80 行内；目录过大时按模块拆段，砍掉纯资源文件。
