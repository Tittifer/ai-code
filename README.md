# AI Code Mother

AI Code Mother 是一个基于 Spring Boot 的 AI 代码生成后端项目，支持通过大模型生成前端页面代码、保存对话历史、执行工作流、部署静态页面，以及通过 SSE 流式返回生成过程。

## 项目特点

- 支持 AI 对话式生成代码
- 支持多种代码生成模式
  - `HTML`
  - `MULTI_FILE`
  - `VUE_PROJECT`
- 支持 LangGraph4j 工作流编排
- 支持 SSE / Flux 流式输出
- 支持 Redis 会话与缓存
- 支持生成后页面部署、截图、代码打包下载
- 支持管理员与普通用户权限控制

## 技术栈

- Java 21
- Spring Boot 3.5.4
- MyBatis-Flex
- MySQL
- Redis
- Redisson
- LangChain4j
- LangGraph4j
- Reactor
- Knife4j / OpenAPI
- Selenium

## 项目结构

```text
src/main/java/com/aicode/codemother
├─ ai               AI 服务与工具调用
├─ controller       接口层
├─ service          业务层
├─ mapper           数据访问层
├─ model            DTO / VO / Entity
├─ langgraph4j      工作流编排
├─ config           Spring 配置
├─ core             核心能力
├─ manager          资源管理
├─ utils            工具类
└─ ratelimter       限流模块
```

## 核心能力

### 1. AI 代码生成

项目支持基于用户提示词生成前端代码，并根据需求自动路由到不同生成模式。

### 2. 工作流编排

通过 LangGraph4j 将以下步骤串起来：

- 图片收集
- 提示词增强
- 智能路由
- 代码生成
- 代码质检
- 项目构建

### 3. 流式输出

项目支持通过 SSE / Flux 实时向前端返回 AI 执行进度。

### 4. 部署与下载

生成后的项目可以：

- 构建为可访问页面
- 生成预览图
- 打包为 zip 下载

## 运行环境

启动前请先准备：

- JDK 21
- Maven 3.9+
- MySQL 8.x
- Redis 6.x 或更高

## 快速开始

### 1. 克隆项目

```bash
git clone <your-repo-url>
cd ai-code-mother-master
```

### 2. 初始化数据库

执行 [sql/create_table.sql](sql/create_table.sql)。

默认数据库名为：

```sql
ai_code_mother
```

如果你本地还没有这个库，可以先执行：

```sql
create database if not exists ai_code_mother character set utf8mb4 collate utf8mb4_unicode_ci;
```

### 3. 启动 Redis

确保本地 Redis 已启动，默认配置为：

- host: `localhost`
- port: `6379`
- database: `0`

### 4. 配置本地参数

本地开发主要看：

- [application.yml](src/main/resources/application.yml)
- [application-local.yml](src/main/resources/application-local.yml)

你至少需要正确配置：

- MySQL 连接
- Redis 连接
- 大模型 API Key
- DashScope 配置
- COS 配置
- Pexels 配置

建议把敏感信息替换成你自己的配置，不要把真实密钥提交到仓库。

### 5. 启动项目

方式一：直接运行主类

- [AiCodeMotherApplication.java](src/main/java/com/aicode/codemother/AiCodeMotherApplication.java)

方式二：使用 Maven

```bash
mvn spring-boot:run
```

## 启动后访问

- 服务地址：`http://localhost:8123/api`
- 接口文档：`http://localhost:8123/api/doc.html`

## 主要接口

### 用户相关

- `POST /api/user/register` 用户注册
- `POST /api/user/login` 用户登录
- `POST /api/user/logout` 用户退出登录
- `GET /api/user/get/login` 获取当前登录用户

### 应用相关

- `POST /api/app/add` 创建应用
- `POST /api/app/update` 修改应用
- `POST /api/app/delete` 删除应用
- `GET /api/app/get/vo` 获取应用详情
- `POST /api/app/my/list/page/vo` 分页获取我的应用
- `GET /api/app/chat/gen/code` AI 流式生成代码
- `POST /api/app/deploy` 部署应用
- `GET /api/app/download/{appId}` 下载应用代码

### 工作流相关

- `POST /api/workflow/execute` 同步执行工作流
- `GET /api/workflow/execute-flux` Flux 流式执行工作流
- `GET /api/workflow/execute-sse` SSE 流式执行工作流

## 配置说明

### 数据源

默认本地数据库使用 MySQL，数据库不存在时，应用会启动失败并报类似错误：

```text
Unknown database 'ai_code_mother'
```

这时需要：

1. 创建数据库
2. 或修改 [application-local.yml](src/main/resources/application-local.yml) 中的 `spring.datasource.url`

### Java 版本

项目 `pom.xml` 中要求：

```xml
<java.version>21</java.version>
```

请尽量使用 JDK 21 运行和编译项目。

## 常见问题

### 1. 启动时报 `Unknown database 'ai_code_mother'`

原因：数据库不存在。  
解决：先创建数据库，再执行建表脚本。

### 2. 启动时报 Redis 连接失败

原因：Redis 没启动或配置不对。  
解决：启动 Redis，并检查 `application-local.yml` 中的 Redis 配置。

### 3. AI 无法调用

原因通常是：

- API Key 未配置
- 模型服务不可用
- 网络不可访问

### 4. 页面部署后无法访问

需要检查：

- 部署目录是否生成
- `deploy-host` 是否正确
- 静态资源服务映射是否正确

## 开发建议

- 本地优先使用 `application-local.yml`
- 不要把真实密钥提交到 Git
- 修改模型相关逻辑时，优先查看 `ai`、`langgraph4j`、`service` 目录
- 修改工作流时，优先查看 `CodeGenWorkflow`、`CodeGenConcurrentWorkflow` 和 `node` 包

## 后续可扩展方向

- 增加更多代码生成模板
- 增加更强的代码质检能力
- 接入更多模型服务商
- 增加前端管理台
- 增加任务队列与异步调度

## License

如需开源分发，建议你根据自己的实际情况补充 License。
