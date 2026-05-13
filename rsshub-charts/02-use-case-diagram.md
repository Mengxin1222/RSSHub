# RSSHub 用例图

## 1. 总体用例图

```mermaid
useCaseDiagram
    actor "普通用户" as User
    actor "管理员" as Admin
    actor "开发者" as Developer
    
    package "RSSHub 系统" {
        usecase "浏览 RSS 源" as UC1
        usecase "生成 RSS 订阅" as UC2
        usecase "过滤 RSS 内容" as UC3
        usecase "使用参数自定义" as UC4
        usecase "配置系统参数" as UC5
        usecase "管理缓存策略" as UC6
        usecase "配置代理设置" as UC7
        usecase "监控系统状态" as UC8
        usecase "开发新路由" as UC9
        usecase "维护现有路由" as UC10
        usecase "提交 Pull Request" as UC11
        usecase "运行测试" as UC12
    }
    
    User --> UC1
    User --> UC2
    User --> UC3
    User --> UC4
    
    Admin --> UC5
    Admin --> UC6
    Admin --> UC7
    Admin --> UC8
    
    Developer --> UC9
    Developer --> UC10
    Developer --> UC11
    Developer --> UC12
```

### 图表解释

#### 1. 整体概述
这张图展示了 RSSHub 系统的整体用例关系，定义了三种主要用户角色：普通用户、管理员和开发者，以及它们与系统功能的交互。RSSHub 是一个开源的 RSS 生成器，致力于让一切皆可 RSS，解决了许多网站不提供 RSS 订阅的问题。

#### 2. 关键元素说明
- **普通用户**：使用 RSSHub 获取各种网站内容的 RSS 订阅的终端用户
- **管理员**：负责部署和维护 RSSHub 实例的运维人员
- **开发者**：为 RSSHub 贡献新路由或修复问题的开源贡献者
- **浏览 RSS 源**：用户访问 RSSHub 提供的各种 RSS 源
- **生成 RSS 订阅**：系统从原始网站抓取内容并生成标准 RSS 格式
- **配置系统参数**：管理员配置 RSSHub 实例的各项参数
- **开发新路由**：开发者为新网站添加 RSS 支持

#### 3. 关键流程说明
- 普通用户通过访问 RSSHub 的路由 URL 获取 RSS 订阅
- 管理员通过环境变量或配置文件管理 RSSHub 实例
- 开发者通过 GitHub 参与项目贡献，提交新路由或修复

#### 4. 关键技术解释
RSSHub 基于 Node.js 和 Hono 框架构建，使用了丰富的依赖库来处理各种网站的抓取需求。系统采用路由注册机制，每个网站对应一个或多个路由，通过统一的中间件处理缓存、错误、访问控制等横切关注点。

#### 5. 设计意图
通过明确的角色划分，RSSHub 设计了清晰的职责边界：普通用户关注内容获取，管理员关注系统稳定性，开发者关注功能扩展。这种分离使系统既能服务广大用户，又能保持开源项目的活跃贡献生态。

---

## 2. 普通用户用例图

```mermaid
useCaseDiagram
    actor "普通用户" as User
    
    package "RSSHub 用户功能" {
        usecase "发现可用路由" as UC1
        usecase "访问 RSS 源" as UC2
        usecase "使用 URL 参数" as UC3
        usecase "过滤内容" as UC4
        usecase "设置热链模板" as UC5
        usecase "调试输出" as UC6
        usecase "订阅到阅读器" as UC7
    }
    
    User --> UC1
    User --> UC2
    User --> UC3
    User --> UC4
    User --> UC5
    User --> UC6
    User --> UC7
    
    UC3 ..> UC2 : <<include>>
    UC4 ..> UC2 : <<include>>
    UC5 ..> UC2 : <<include>>
    UC6 ..> UC2 : <<include>>
    UC7 ..> UC2 : <<extend>>
```

### 图表解释

#### 1. 整体概述
这张图详细展示了普通用户与 RSSHub 交互的具体用例，涵盖了从发现路由到最终订阅到 RSS 阅读器的完整用户旅程。普通用户是 RSSHub 最主要的使用者群体，他们通过简单的 URL 访问即可获取各类网站的 RSS 订阅。

#### 2. 关键元素说明
- **发现可用路由**：用户通过 RSSHub 文档或雷达浏览器扩展发现支持的网站
- **访问 RSS 源**：核心用例，用户直接访问路由 URL 获取 RSS 内容
- **使用 URL 参数**：用户通过 URL 查询参数自定义 RSS 输出（如 limit、filter 等）
- **过滤内容**：用户使用 filter 参数过滤 RSS 条目
- **设置热链模板**：用户配置图片反代模板解决防盗链问题
- **调试输出**：使用 debug 参数获取调试信息
- **订阅到阅读器**：将 RSS 源添加到 RSS 阅读器（如 Folo、Inoreader 等）

#### 3. 关键流程说明
用户首先通过各种渠道发现 RSSHub 支持的路由，然后构造相应的 URL 访问 RSS 源。在访问过程中，用户可以添加各种 URL 参数来自定义输出。最后，用户可以将这个 RSS 源订阅到自己喜爱的 RSS 阅读器中，实现内容的自动更新推送。

#### 4. 关键技术解释
RSSHub 提供了丰富的 URL 参数支持：
- `limit`：限制返回条目数量
- `filter`/`filterout`：通过正则表达式过滤条目
- `filter_title`/`filter_description`：针对特定字段过滤
- `debug`：启用调试模式查看原始数据
- `hotlink`：自定义图片反代模板

这些参数通过 Hono 框架的请求上下文传递给各个路由处理器。

#### 5. 设计意图
RSSHub 采用"配置即代码"的理念，将所有自定义选项都暴露为 URL 参数，这样用户无需部署自己的实例就能享受到个性化的 RSS 服务。这种设计大大降低了使用门槛，让普通用户也能轻松使用 RSSHub 的高级功能。

---

## 3. 管理员用例图

```mermaid
useCaseDiagram
    actor "管理员" as Admin
    
    package "RSSHub 管理功能" {
        usecase "部署 RSSHub 实例" as UC1
        usecase "配置环境变量" as UC2
        usecase "配置缓存系统" as UC3
        usecase "设置代理服务" as UC4
        usecase "配置访问控制" as UC5
        usecase "启用错误追踪" as UC6
        usecase "监控系统指标" as UC7
        usecase "配置路由 Cookie" as UC8
        usecase "管理 NSFW 内容" as UC9
        usecase "配置 OpenAI 集成" as UC10
    }
    
    Admin --> UC1
    Admin --> UC2
    Admin --> UC3
    Admin --> UC4
    Admin --> UC5
    Admin --> UC6
    Admin --> UC7
    Admin --> UC8
    Admin --> UC9
    Admin --> UC10
    
    UC2 ..> UC1 : <<include>>
    UC3 ..> UC1 : <<include>>
    UC4 ..> UC1 : <<include>>
    UC5 ..> UC1 : <<include>>
    UC8 ..> UC2 : <<include>>
    UC9 ..> UC2 : <<include>>
    UC10 ..> UC2 : <<include>>
```

### 图表解释

#### 1. 整体概述
这张图展示了管理员在部署和维护 RSSHub 实例时的各项操作。管理员负责确保 RSSHub 实例的稳定运行、性能优化和安全配置，是私有化部署场景下的关键角色。

#### 2. 关键元素说明
- **部署 RSSHub 实例**：通过 Docker、Vercel、Fly.io 等方式部署 RSSHub
- **配置环境变量**：设置 100+ 个环境变量来定制 RSSHub 行为
- **配置缓存系统**：选择内存、Redis 或 HTTP 缓存，设置缓存过期时间
- **设置代理服务**：配置 HTTP/HTTPS/SOCKS 代理或 PAC 脚本
- **配置访问控制**：设置 access key 限制实例访问
- **启用错误追踪**：集成 Sentry 或 Honeybadger 进行错误监控
- **监控系统指标**：通过 /metrics 端点查看 Prometheus 指标
- **配置路由 Cookie**：为需要登录的网站配置 Cookie 或 API Key
- **管理 NSFW 内容**：启用 DISABLE_NSFW 选项过滤成人内容
- **配置 OpenAI 集成**：启用 AI 摘要和翻译功能

#### 3. 关键流程说明
管理员首先选择合适的部署方式部署 RSSHub 实例，然后根据需要配置各种环境变量。核心配置包括缓存策略（减少对源站的请求压力）、代理设置（解决地区限制问题）、以及特定路由的认证信息。部署完成后，管理员通过监控端点持续观察系统运行状态。

#### 4. 关键技术解释
RSSHub 的配置系统完全基于环境变量，支持：
- **缓存类型**：memory（内存 LRU 缓存）、redis、http 或禁用
- **代理策略**：all（所有请求）或 on_retry（仅失败重试时）
- **多代理支持**：通过 PROXY_URIS 配置多个代理自动故障转移
- **远程配置**：支持从远程 URL 加载配置，支持 BASIC 认证
- **动态 Cookie**：支持为 Bilibili 等站点配置多组 Cookie 轮询使用

#### 5. 设计意图
RSSHub 的管理员功能设计遵循"约定优于配置"原则，提供了合理的默认值，让简单部署变得容易。同时，通过丰富的环境变量选项，满足了高级用户的定制需求。这种设计使 RSSHub 既能作为公共服务运行，也能作为私有实例安全部署。

---

## 4. 开发者用例图

```mermaid
useCaseDiagram
    actor "开发者" as Developer
    
    package "RSSHub 开发功能" {
        usecase "Fork 项目仓库" as UC1
        usecase "搭建开发环境" as UC2
        usecase "阅读贡献指南" as UC3
        usecase "开发新路由" as UC4
        usecase "编写路由文档" as UC5
        usecase "维护现有路由" as UC6
        usecase "运行测试套件" as UC7
        usecase "提交 Pull Request" as UC8
        usecase "代码审查与修改" as UC9
        usecase "发布新版本" as UC10
        usecase "调试 Puppeteer" as UC11
    }
    
    Developer --> UC1
    Developer --> UC2
    Developer --> UC3
    Developer --> UC4
    Developer --> UC5
    Developer --> UC6
    Developer --> UC7
    Developer --> UC8
    Developer --> UC9
    Developer --> UC10
    Developer --> UC11
    
    UC4 ..> UC3 : <<include>>
    UC5 ..> UC4 : <<include>>
    UC7 ..> UC4 : <<include>>
    UC7 ..> UC6 : <<include>>
    UC9 ..> UC8 : <<extend>>
    UC11 ..> UC4 : <<extend>>
    UC11 ..> UC6 : <<extend>>
```

### 图表解释

#### 1. 整体概述
这张图展示了开发者参与 RSSHub 开源项目的完整工作流程。RSSHub 拥有活跃的开源社区，每周都有新的路由被贡献，开发者是保持 RSSHub 生命力的核心力量。

#### 2. 关键元素说明
- **Fork 项目仓库**：在 GitHub 上 Fork DIYgod/RSSHub 仓库
- **搭建开发环境**：克隆代码、安装依赖、启动开发服务器
- **阅读贡献指南**：了解路由开发规范和代码风格
- **开发新路由**：为新网站创建 RSS 路由（最常见的贡献）
- **编写路由文档**：在 docs 目录添加路由使用说明
- **维护现有路由**：修复失效的路由或更新功能
- **运行测试套件**：执行 vitest 测试确保代码质量
- **提交 Pull Request**：将改动提交回上游仓库
- **代码审查与修改**：响应维护者的审查意见进行修改
- **发布新版本**：项目维护者发布 npm 包和 Docker 镜像
- **调试 Puppeteer**：使用 Puppeteer 调试复杂的动态页面

#### 3. 关键流程说明
开发者首先 Fork 项目并搭建本地开发环境，然后选择一个网站开发新路由或修复现有问题。开发完成后，编写相应的文档，运行测试确保没有问题，最后提交 Pull Request。项目维护者会进行代码审查，可能要求一些修改，通过后代码会合入主分支。

#### 4. 关键技术解释
RSSHub 的路由开发体系：
- **目录结构**：lib/routes/{namespace}/ 下放置路由文件
- **路由定义**：使用 `namespace` 和 `route` 导出
- **工具函数**：提供了 `ofetch`、`cheerio`、`got` 等丰富的工具
- **Puppeteer 支持**：内置对动态渲染页面的支持
- **测试框架**：使用 vitest 进行单元测试和路由测试
- **构建系统**：自动构建路由注册表和文档

#### 5. 设计意图
RSSHub 的开发者体验设计注重降低贡献门槛，通过清晰的目录结构、完善的工具函数、详细的贡献指南，让即使是新手也能轻松贡献新路由。同时，通过自动化测试和严格的代码审查，保证了代码质量和项目的长期可维护性。

---

## 5. 路由生态系统用例图

```mermaid
useCaseDiagram
    actor "用户" as User
    actor "路由维护者" as Maintainer
    actor "RSSHub Radar" as Radar
    actor "第三方阅读器" as Reader
    
    package "RSSHub 生态系统" {
        usecase "发现新路由" as UC1
        usecase "订阅 RSS 源" as UC2
        usecase "自动更新路由" as UC3
        usecase "检测网站 RSS" as UC4
        usecase "一键订阅" as UC5
        usecase "提交路由失效" as UC6
        usecase "修复失效路由" as UC7
        usecase "添加新网站支持" as UC8
        usecase "使用 AI 摘要" as UC9
        usecase "跨平台使用" as UC10
    }
    
    User --> UC1
    User --> UC2
    User --> UC6
    User --> UC9
    User --> UC10
    
    Maintainer --> UC7
    Maintainer --> UC8
    
    Radar --> UC4
    Radar --> UC5
    Radar --> UC1
    
    Reader --> UC2
    Reader --> UC3
    Reader --> UC10
    
    UC5 ..> UC2 : <<include>>
    UC7 ..> UC6 : <<extend>>
    UC4 ..> UC1 : <<include>>
    UC9 ..> UC2 : <<extend>>
```

### 图表解释

#### 1. 整体概述
这张图展示了 RSSHub 完整的生态系统，包括核心服务、浏览器扩展、第三方阅读器以及社区维护者之间的协作关系。RSSHub 不仅仅是一个项目，而是一个完整的 RSS 生态。

#### 2. 关键元素说明
- **RSSHub Radar**：官方浏览器扩展，自动检测当前网站是否有对应的 RSSHub 路由
- **第三方阅读器**：如 Folo、Miniflux、RSSBud 等与 RSSHub 深度集成的应用
- **路由维护者**：专门负责维护特定网站路由的贡献者
- **发现新路由**：用户通过 Radar 或文档发现新支持的网站
- **自动更新路由**：阅读器定期更新 RSSHub 路由注册表
- **检测网站 RSS**：Radar 在浏览时自动检测可用的 RSS 源
- **一键订阅**：Radar 提供的一键添加到阅读器功能
- **提交路由失效**：用户通过 Issue 报告失效的路由
- **使用 AI 摘要**：利用 OpenAI 集成自动生成内容摘要和翻译

#### 3. 关键流程说明
用户安装 RSSHub Radar 浏览器扩展后，在浏览网页时会自动检测该网站是否有对应的 RSSHub 路由。如果有，用户可以一键订阅到自己的阅读器。当某个路由失效时，用户可以提交 Issue，路由维护者会修复问题。整个生态系统通过持续的更新保持活力。

#### 4. 关键技术解释
RSSHub 生态系统的关键技术：
- **路由注册表**：构建时自动生成的 routes.json，包含所有路由元数据
- **Radar 规则**：每个路由可以定义 radar 规则，指定在哪些网站上自动检测
- **RSS 标准**：输出标准的 RSS 2.0 或 Atom 格式，兼容所有阅读器
- **AI 集成**：使用 OpenAI API 提供智能摘要、翻译等增强功能
- **多端适配**：支持部署到 Docker、Vercel、Fly.io、Cloudflare Workers 等多种平台

#### 5. 设计意图
RSSHub 生态系统的设计目标是让 RSS 重新变得简单易用。通过 Radar 等工具降低了发现和使用 RSSHub 的门槛，通过活跃的社区维护确保了路由的时效性，通过开放的架构鼓励第三方集成，最终构建了一个可持续发展的 RSS 生态。

---
*生成时间: 2026-05-13*
*模式: 深度*
*所属项目: RSSHub*
*文件包含: 5 张用例图*
