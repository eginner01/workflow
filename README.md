# Workflow 插件

`workflow` 是 FastAPI Best Architecture 的企业审批流后端插件，提供流程定义、流程实例、审批任务、消息通知与申请侧能力。

## 功能概览

- 流程分类与流程定义管理
- 流程实例发起、审批、撤回、催办
- 待办任务与消息通知能力
- 已发布流程的 applicant-only 访问能力
- WebSocket 按用户消息推送
- MySQL / PostgreSQL 初始化与销毁 SQL

## 目录结构

- `api/`：插件 API 路由
- `crud/`：数据访问层
- `engine/`：流程相关运行时能力
- `model/`：数据库模型
- `schema/`：请求与响应结构
- `service/`：业务逻辑层
- `sql/`：MySQL / PostgreSQL 初始化与清理脚本
- `tasks/`：插件任务入口
- `plugin.toml`：插件元数据

## 插件元数据

当前插件元数据定义在 `plugin.toml` 中：

- 名称：`workflow`
- 版本：`0.1.0`
- 标签：`task`、`notification`
- 数据库：`mysql`、`postgresql`
- 作者：`eginner01`

## 安装示例

以下示例用于说明宿主工程如何接入该插件，具体命令请结合 FBA 当前插件安装流程执行。

### 1. 安装插件代码

如果通过插件市场安装，宿主工程会将插件放入对应插件目录。

如果手动接入，可将本仓库内容放入宿主工程插件目录，例如：

```text
backend/plugin/workflow/
```

> 注意：应拷贝本仓库中的文件内容，而不是在目标目录外再额外包一层 `workflow/`。

### 2. 执行数据库初始化 SQL

根据所用数据库执行对应脚本：

- MySQL：`sql/mysql/init.sql`
- PostgreSQL：`sql/postgresql/init.sql`
- 如使用 snowflake 主键方案，则执行对应 `init_snowflake.sql`

### 3. 加载插件路由

插件元数据中已声明：

```toml
[app]
router = ["v1"]
```

宿主工程应按 FBA 插件加载机制加载插件路由。

### 4. 初始化菜单与权限数据

如需在系统中展示 workflow 菜单，还需要补充：

- workflow 菜单初始化 SQL
- 角色菜单授权数据
- 测试账号权限种子数据

### 5. 与前端插件配套接入

建议同时安装前端插件 `workflow_ui`，否则仅后端接口可用，无法直接获得流程设计器与业务页面。

## 宿主工程接入说明

在宿主工程中，接入该插件时通常需要关注以下几点：

### 路由接入
- 按插件机制加载 `api/router.py`
- 确保 workflow 路由暴露在统一 API 前缀下

### 数据模型
- 确保插件模型能被宿主工程识别并参与建表或迁移流程
- 若使用插件 SQL，而非 Alembic 管理，则需保证 SQL 与当前数据库版本一致

### 权限体系
- workflow 的管理态接口应保留在管理权限范围内
- 普通用户仅应暴露申请侧能力

### 消息与实时通知
- 若宿主工程启用了 WebSocket / Socket.IO，需保证 workflow 消息通知入口已接入
- 若未启用实时通道，审批流仍可工作，但消息联动能力会受限

## SQL 说明

`sql/` 目录包含以下脚本：

- `sql/mysql/init.sql`
- `sql/mysql/destroy.sql`
- `sql/mysql/init_snowflake.sql`
- `sql/mysql/destroy_snowflake.sql`
- `sql/postgresql/init.sql`
- `sql/postgresql/destroy.sql`
- `sql/postgresql/init_snowflake.sql`
- `sql/postgresql/destroy_snowflake.sql`

> 注意：当前销毁脚本已调整为避免误删不应删除的业务表，使用前仍建议先确认目标环境。

## 权限模型

本插件区分管理侧与申请侧：

- 管理侧：流程定义、分类与管理能力
- 申请侧：普通用户发起已发布流程、查看进度、撤回、催办

普通用户不应依赖管理态流程定义接口。

## 配套前端插件

前端插件仓库：`workflow_ui`

两者应配套使用：

- `workflow` 提供接口、模型、权限与运行时能力
- `workflow_ui` 提供设计器、申请页、列表页、详情页与消息相关页面

## 当前状态

该仓库当前主要用于插件发布与插件市场接入整理，后续可继续完善：

- 更完整的安装文档
- 宿主工程接入步骤
- 权限与菜单初始化示例
- 前后端联调说明
