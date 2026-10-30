# note 目录结构
real_dir: D:\pro\note_project\note
generated_at: 2026-10-30 20:05
commit: 917f19e

> 本文件只覆盖**后端**仓库。前端是独立仓库 `D:\pro\note_project\note_for_android`（Flutter），不在本 real_dir 下，改前端接口时另需扫描该目录。

tree: |
  note/
  ├── pom.xml                    # 父 pom：groupId com.chbcraft / artifactId noteback，Spring Boot 3.3.4
  │                              #   modules: gateway, note, userInfo, dto, auth
  ├── gateway-module/            # 包 com.chbcraft.gateway
  │   └── src/main/java/…/gateway/{config,filter,utils}/  + GatewayMainApplication.java
  ├── auth-module/               # 包 com.chbcraft.auth（001-split-auth 新建）
  │   └── src/main/java/…/auth/{config,controller,interceptor,mapper,service,util}/ + AuthMainApplication.java
  │   └── src/test/java/…/auth/{interceptor,service,util}/   # 3 个测试类
  ├── userInfo-module/           # 包 com.chbcraft.userinfo
  │   └── src/main/java/…/userinfo/{config,…}/
  ├── dto-module/                # 包 com.chcbcraft.dto  ← 拼写多一个 c，见「约定」
  │   ├── src/main/java/…/dto/{context,dto,enums,vo}/    # Note/Notebook/User/Tag 等实体 + Request/Result
  │   └── src/test/java/com/chcbcraft/dto/{context,enums}/
  ├── note-module/               # 包 org.template.*  ← 脚手架默认包名，未改名
  │   ├── pom.xml                # 注意：parent 被注释掉，未继承父 pom
  │   ├── src/main/java/org/template/{config,controller,enums,exceptions,handler,interceptor,mapper,model,service}/
  │   ├── src/main/resources/    # application.yml, lua/, mapper/, plugins.properties
  │   ├── config-center/         # datasource.properties, minio.properties, redis-config.properties
  │   ├── sql/                   # init_schema.sql, notebook.sql
  │   ├── note.sql
  │   └── *.md                   # bugs.md, redis-cache-design.md, redis-pageList.md, 安全策略总结.md（设计笔记）
  └── 架构设计.txt

## 关键路径速查
- 各服务入口: `gateway-module/…/GatewayMainApplication.java`、`auth-module/…/AuthMainApplication.java`
- 共享 DTO/实体: `dto-module/src/main/java/com/chcbcraft/dto/`（跨模块 import 都走这里）
- 网关鉴权: `gateway-module/…/filter/GlobalJwtFilter.java` + `config/AuthConfig.java`
- JWT 工具: `auth-module/…/util/JwtUtil.java` 与 `gateway-module/…/utils/JwtUtils.java`（两份，改一处记得看另一份）
- 测试: JUnit，`src/test/java` 下按包镜像；全量 `mvn test`，单模块 `mvn -pl <module> test`
- 构建: **必须用 `D:/tools/apache-maven-3.9.9/bin/mvn.cmd`**（PATH 里的 3.6.2 太旧，见 knowledge）
- 运行: 各模块 Spring Boot 独立启动；配置在 `note-module/config-center/*.properties` 与 `application.yml`

## 约定（写代码前必读）
- **包名三个模块三种写法，别靠肌肉记忆**：
  | 模块 | 实际包名 |
  |---|---|
  | dto-module | `com.chcbcraft.dto.*`（c-h-c-b，多一个 c） |
  | auth / gateway / userInfo | `com.chbcraft.<module>.*`（与 groupId 一致） |
  | note-module | `org.template.*`（脚手架遗留） |
  新建文件前先 `sed -n '/^package/p' <同目录已有文件>` 核对，别照 groupId 推。
- 新建跨模块可见的 Request/VO/枚举 → 放 `dto-module`，不要在业务模块重复定义。
- 分层沿用现有 `controller → service → mapper`，DTO 转换在 service 层。
