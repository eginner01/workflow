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

## 安装与接入说明

该仓库用于以 FBA 插件形式发布后端审批流能力。接入时请结合宿主工程的插件安装流程使用。

安装后需至少完成以下事项：

1. 加载插件路由
2. 执行对应数据库的初始化 SQL
3. 补充菜单、权限与测试数据种子
4. 与前端插件 `workflow_ui` 配套接入

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
