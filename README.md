# Sirius Scan v0.4.0

![Sirius Scan 控制面板](/documentation/dash-dark.gif)

Sirius是一个开源的综合漏洞扫描器，利用社区驱动的安全情报和自动化渗透测试能力。**v0.4.0**版本引入了全面的系统监控和可观察性功能。通过我们基于Docker的设置，您可以在几分钟内开始使用。

## 🚀 快速开始指南

### 前提条件

- **Docker Engine** 20.10.0+ 并带有 Docker Compose V2
- **系统要求**：最低4GB RAM，10GB可用磁盘空间
- **网络访问**：需要互联网连接用于漏洞数据库更新
- **支持平台**：Linux、macOS、Windows（需WSL2）

### ⚡ 一键安装

```bash
# 克隆并启动 Sirius（使用 GitHub Container Registry 中的预构建镜像）
git clone https://github.com/purpose168/Sirius-cn.git
cd Sirius
docker compose up -d

# 访问Web界面
open http://localhost:3000
```

**注意**：默认情况下，Sirius 使用来自 GitHub Container Registry 的预构建容器镜像以实现快速部署（5-8分钟）。如果需要本地开发并修改源代码，请使用 `docker compose -f docker-compose.yaml -f docker-compose.dev.yaml up -d`。

**登录凭据**：

- 用户名：`admin`
- 密码：`password`

**⚠️ 安全提示**：在生产环境中立即更改这些默认凭据。

## 🆕 v0.4.0 新功能

### 系统监控与可观察性

- **实时健康监控**：所有组件的实时服务健康检查
- **集中式日志**：统一的日志收集和管理系统
- **性能指标**：容器资源利用率跟踪
- **系统控制面板**：位于 `/system-monitor` 的综合监控界面

### 增强的可靠性

- **改进的容器构建**：生产就绪的 Docker 配置
- **更好的错误处理**：全面的错误管理和恢复
- **SSH 故障排除**：增强的部署调试功能
- **自动化测试**：强大的容器测试和验证

### 🔧 安装选项

#### 选项1：标准安装（推荐给大多数用户）

默认配置提供完整的扫描环境：

```bash
git clone https://github.com/purpose168/Sirius-cn.git
cd Sirius
docker compose up -d
```

#### 选项2：用户友好安装（简化版）

提供最简洁的体验，不包含开发工具：

```bash
git clone https://github.com/purpose168/Sirius-cn.git
cd Sirius
docker compose -f docker-compose.user.yaml up -d
```

#### 选项3：生产部署

适用于性能优化的生产环境：

```bash
git clone https://github.com/purpose168/Sirius-cn.git
cd Sirius
docker compose -f docker-compose.production.yaml up -d
```

### ✅ 验证安装

```bash
# 检查所有服务是否运行
docker ps

# 预期服务：
# - sirius-ui (端口 3000)
# - sirius-api (端口 9001)
# - sirius-engine (端口 5174, 50051)
# - sirius-postgres (端口 5432)
# - sirius-rabbitmq (端口 5672, 15672)
# - sirius-valkey (端口 6379)

# 访问Web界面
curl http://localhost:3000

# 检查API健康状态
curl http://localhost:9001/health
```

## 🎯 Sirius 能做什么？

### 核心功能

- **🔍 网络发现**：自动主机发现和服务枚举
- **🛡️ 漏洞评估**：基于CVE的漏洞检测，带有CVSS评分
- **📊 风险管理**：全面的风险评分和修复指导
- **🎪 可视化扫描工作流**：拖放式扫描配置
- **🔄 自动化扫描**：定时和持续的安全评估
- **📡 远程代理支持**：跨多个环境的分布式扫描
- **💻 交互式终端**：基于PowerShell的命令界面，用于高级操作
- **📈 实时仪表板**：实时扫描进度和漏洞指标

### 支持的扫描类型

- **网络扫描**：基于Nmap的端口和服务发现
- **漏洞扫描**：基于NSE脚本的漏洞检测
- **SMB/Windows评估**：专门的Windows安全测试
- **自定义工作流**：用户定义的扫描配置
- **基于代理的扫描**：远程端点评估

## 🏗️ 系统架构

Sirius 采用微服务架构，包含以下组件：

| 服务名称             | 描述             | 技术栈                     | 端口       | 用途                               |
| ------------------- | ----------------------- | ------------------------------ | ----------- | ------------------------------------- |
| **sirius-ui**       | Web 前端            | Next.js 14, React, TailwindCSS | 3000        | 用户界面和可视化      |
| **sirius-api**      | REST API 后端        | Go, Gin 框架              | 9001        | API 端点和业务逻辑      |
| **sirius-engine**   | 多服务容器 | Go, Air 热重载            | 5174, 50051 | 扫描器、终端和代理服务 |
| **sirius-postgres** | 主数据库        | PostgreSQL 15                  | 5432        | 漏洞和扫描数据存储   |
| **sirius-rabbitmq** | 消息队列           | RabbitMQ                       | 5672, 15672 | 服务间通信           |
| **sirius-valkey**   | 缓存层             | Redis兼容               | 6379        | 会话和临时数据            |

### 📡 服务通信流程

```
用户界面 (sirius-ui)
    ↓ HTTP/WebSocket
REST API (sirius-api)
    ↓ AMQP 消息
消息队列 (sirius-rabbitmq)
    ↓ 队列处理
扫描引擎 (sirius-engine)
    ↓ SQL 查询
数据库 (sirius-postgres)
```

### 🗄️ 数据存储

- **PostgreSQL**：漏洞数据、扫描结果、主机信息
- **SQLite**：用户认证和会话数据（开发环境）
- **Valkey/Redis**：缓存、临时扫描数据、会话存储
- **RabbitMQ**：扫描请求和代理通信的消息队列

## 📱 界面概述

### 📊 控制面板

![Sirius Scan 控制面板](/documentation/dash-dark.gif)

您的中央指挥中心，具有以下功能：

- 实时扫描活动和进度监控
- 最新漏洞发现及严重程度趋势
- 系统性能指标和资源利用率
- 常用扫描操作的快速访问控制
- 带有风险评分的执行摘要

### 🔍 扫描界面

![扫描界面](/documentation/scanner.jpg)

高级扫描功能：

- **可视化工作流编辑器**：拖放式扫描模块配置
- **实时进度**：带详细日志的实时扫描状态
- **自定义配置文件**：保存和重用扫描配置
- **计划扫描**：支持类似cron的自动化扫描调度
- **多目标支持**：扫描多个主机、网络或IP范围
- **NSE脚本集成**：用于专门测试的自定义Nmap脚本

### 🎯 漏洞导航器

![漏洞导航器](/documentation/vulnerability-navigator.jpg)

全面的漏洞管理：

- **动态过滤**：跨所有漏洞数据的实时搜索
- **风险优先级**：基于CVSS的严重程度排序和过滤
- **详细报告**：CVE/CPE映射及修复指导
- **导出功能**：PDF、CSV和JSON报告生成
- **历史跟踪**：漏洞时间线和修复进度
- **集成就绪**：用于外部安全工具的API端点

### 🌐 环境概览

![环境概览](/documentation/environment.jpg)

完整的基础设施可见性：

- **资产清单**：全面的主机和服务发现
- **网络拓扑**：已发现基础设施的交互式可视化
- **风险评估**：环境范围的安全态势分析
- **服务枚举**：详细的服务版本和配置信息
- **合规跟踪**：安全基线监控和报告

### 🖥️ 主机详情

![主机详情](/documentation/host.jpg)

深入的系统分析：

- **系统分析**：完整的硬件和软件清单
- **端口分析**：详细的服务发现和版本检测
- **安全指标**：特定主机的漏洞数量和风险评分
- **历史数据**：扫描历史和安全趋势分析
- **修复跟踪**：修复验证和安全改进监控

### 💻 终端界面

![终端界面](/documentation/terminal.jpg)

高级操作控制台：

- **PowerShell环境**：完整的自动化脚本功能
- **代理管理**：远程代理部署和配置
- **自定义脚本**：执行自定义安全测试脚本
- **批量操作**：批量扫描和管理操作
- **系统诊断**：实时系统健康和性能监控

## 🛠️ 标准安装

非常适合安全专业人员和渗透测试人员：

```bash
git clone https://github.com/purpose168/Sirius-cn.git
cd Sirius
docker compose up -d
```

此配置提供：

- ✅ 开箱即用的完整扫描功能
- ✅ 预配置的漏洞数据库
- ✅ 无需额外设置
- ✅ 生产就绪的安全扫描

## 🤝 贡献

想要为Sirius做出贡献？我们欢迎社区的贡献！

**对于开发者**：查看我们全面的[贡献指南](./documentation/contributing.md)，了解：

- 🔧 开发环境设置
- 🔄 开发工作流和最佳实践
- 🧪 测试和质量保证
- 📝 代码标准和Git工作流
- 🚀 提交拉取请求

**快速链接**：
- [开发设置](./documentation/contributing.md#development-environment-setup)
- [测试指南](./documentation/contributing.md#testing--quality-assurance)
- [代码标准](./documentation/contributing.md#code-standards)
- [GitHub Issues](https://github.com/purpose168/Sirius-cn/issues)

加入我们的社区，帮助使安全扫描对每个人都可访问！

## 🔌 API 与集成

Sirius 提供全面的 API，用于与现有安全工作流集成：

### REST API 端点

- **认证**：`/api/auth` - 基于 JWT 的认证
- **主机**：`/api/hosts` - 主机管理和发现
- **扫描**：`/api/scans` - 扫描管理和执行
- **漏洞**：`/api/vulnerabilities` - 漏洞数据访问
- **报告**：`/api/reports` - 报告生成和导出

### WebSocket API

- **实时更新**：实时扫描进度和漏洞通知
- **代理通信**：双向代理管理
- **系统监控**：实时系统指标和健康状态

### 集成示例

```bash
# 通过 API 启动网络扫描
curl -X POST http://localhost:9001/api/scans \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"target": "192.168.1.0/24", "scan_type": "network"}'

# 获取漏洞摘要
curl http://localhost:9001/api/vulnerabilities/summary \
  -H "Authorization: Bearer $TOKEN"

# 导出扫描结果
curl http://localhost:9001/api/reports/scan/123/pdf \
  -H "Authorization: Bearer $TOKEN" \
  -o scan-report.pdf
```

## 🔧 故障排除

### 常见问题与解决方案

#### 🐳 容器问题

**问题**：服务无法启动

```bash
# 诊断
docker compose ps              # 检查服务状态
docker compose logs <service>  # 查看服务日志
docker system df              # 检查磁盘空间

# 解决方案
docker compose down && docker compose up -d --build  # 全新重启
docker system prune -f                               # 清理空间
```

**问题**：基础设施服务（PostgreSQL、RabbitMQ、Valkey）无法启动

```bash
# 当仅使用 docker-compose.dev.yaml 时会发生此问题
# dev 文件是覆盖文件，不是独立文件

# ❌ 错误（仅启动 3 个服务）：
docker compose -f docker-compose.dev.yaml up -d

# ✅ 正确（启动所有 6 个服务）：
docker compose -f docker-compose.yaml -f docker-compose.dev.yaml up -d
```

**问题**："端口已被占用"错误

```bash
# 查找占用端口的进程
netstat -tuln | grep 3000
lsof -i :3000

# 解决方案：停止冲突服务或更改端口
docker compose down
# 如有需要，编辑 docker-compose.yaml 使用不同端口
```

#### 🔍 扫描器问题

**问题**：Nmap 错误或扫描失败

```bash
# 检查扫描器日志
docker logs sirius-engine | grep -i nmap

# 直接测试 Nmap
docker exec sirius-engine nmap --version
docker exec sirius-engine nmap -p 80 127.0.0.1

# 常见修复
docker restart sirius-engine
docker exec sirius-engine which nmap  # 验证 Nmap 安装
```

**问题**："端口规格重复"警告

```bash
# 当前版本已解决此问题，但如果您看到此警告：
docker exec sirius-engine grep -r "port.*specification" /app-scanner-src/
# 应显示已修正的端口范围，如 "1-1000,3389"
```

#### 🗄️ 数据库问题

**问题**：数据库连接失败

```bash
# 检查 PostgreSQL 状态
docker exec sirius-postgres pg_isready
docker logs sirius-postgres

# 测试连接
docker exec sirius-postgres psql -U postgres -d sirius -c "SELECT version();"

# 如有需要，重置数据库
docker compose down
docker volume rm sirius_postgres_data
docker compose up -d
```

#### 🐰 消息队列问题

**问题**：RabbitMQ 连接问题

```bash
# 检查 RabbitMQ 状态
docker exec sirius-rabbitmq rabbitmqctl status

# 查看队列状态
docker exec sirius-rabbitmq rabbitmqctl list_queues

# 访问管理界面
open http://localhost:15672  # 用户名/密码: guest/guest
```

**问题**：RabbitMQ 架构完整性检查失败

```bash
# 当 RabbitMQ 有来自不兼容版本的旧数据时会发生此问题
# 解决方案：删除旧卷并重新启动

docker compose down -v  # 对于标准安装
# 对于开发环境：
docker compose -f docker-compose.yaml -f docker-compose.dev.yaml down -v
docker compose -f docker-compose.yaml -f docker-compose.dev.yaml up -d
```

#### 🌐 网络与连接

**问题**：服务无法通信

```bash
# 测试内部网络
docker exec sirius-ui ping sirius-api
docker exec sirius-api ping sirius-postgres

# 检查网络配置
docker network ls
docker network inspect sirius_default
```

**问题**：外部访问问题

```bash
# 验证端口映射
docker port sirius-ui
docker port sirius-api

# 检查防火墙（Linux）
sudo ufw status
sudo iptables -L

# 检查防火墙（macOS）
sudo pfctl -s all
```

### 🚨 紧急恢复

**完整系统重置**：

```bash
# 停止所有服务
docker compose down

# 删除所有数据（⚠️ 这将删除所有扫描数据！）
docker compose down -v

# 清理 Docker 系统
docker system prune -a -f

# 全新启动
docker compose up -d --build
```

**备份当前数据**：

```bash
# 备份数据库
docker exec sirius-postgres pg_dump -U postgres sirius > backup.sql

# 备份扫描结果目录
docker cp sirius-engine:/opt/sirius/ ./sirius-backup/
```

## 🔒 安全最佳实践

### 🏭 生产部署

**必要的安全步骤**：

1. **更改默认凭据**：

```bash
# 在 docker-compose.production.yaml 中更新
POSTGRES_PASSWORD=your_secure_password
RABBITMQ_DEFAULT_PASS=your_secure_password
NEXTAUTH_SECRET=your_long_random_secret
```

2. **网络安全**：

```bash
# 使用内部网络进行服务通信
# 仅暴露必要端口（UI 使用 3000）
# 配置防火墙规则
sudo ufw allow 3000/tcp
sudo ufw deny 5432/tcp  # 不要暴露数据库
```

3. **SSL/TLS 配置**：

```bash
# 使用带 SSL 的反向代理（nginx/traefik）
# 为 Web 界面启用 HTTPS
# 使用适当的证书保护 API 端点
```

4. **数据保护**：

```bash
# 加密数据库备份
# 安全的卷挂载
# 定期安全更新
docker compose pull  # 定期更新镜像
```

### 🛡️ 安全扫描最佳实践

- **网络隔离**：尽可能从隔离网络运行扫描
- **权限管理**：为扫描账户使用最小权限原则
- **扫描调度**：在维护窗口期间执行密集扫描
- **数据保留**：实施适当的数据生命周期策略
- **审计日志**：启用全面日志记录以满足合规要求

## 📚 文档与资源

### 📖 核心文档

- [📘 安装指南](https://sirius.publickey.io/docs/getting-started/installation) - 详细的设置说明
- [🎯 快速开始指南](https://sirius.publickey.io/docs/getting-started/quick-start) - 5分钟内开始扫描
- [🎪 界面导览](https://sirius.publickey.io/docs/getting-started/interface-tour) - 完整的UI浏览
- [🔧 配置指南](https://sirius.publickey.io/docs/guides/configuration) - 高级配置选项
- [🛡️ 安全指南](https://sirius.publickey.io/docs/guides/security) - 生产环境安全最佳实践

### 🔌 技术文档

- [🚀 API 参考](https://sirius.publickey.io/docs/api/rest/authentication) - 完整的API文档
- [📦 Go SDK](https://sirius.publickey.io/docs/api/sdk/go) - Go集成库
- [🐳 Docker 指南](./documentation/DOCKER-IMPLEMENTATION-DOCUMENTATION.md) - 全面的Docker文档
- [🏗️ 架构指南](./documentation/README.architecture.md) - 系统架构深度解析
- [🔄 CI/CD 指南](./documentation/README.cicd.md) - 部署自动化

### 🎓 用户指南

- [🔍 扫描指南](https://sirius.publickey.io/docs/guides/scanning) - 高级扫描技术
- [🎯 漏洞管理](https://sirius.publickey.io/docs/guides/vulnerabilities) - 管理发现的漏洞
- [🌐 环境管理](https://sirius.publickey.io/docs/guides/environment) - 基础设施评估
- [🖥️ 主机管理](https://sirius.publickey.io/docs/guides/hosts) - 单个主机分析
- [💻 终端指南](https://sirius.publickey.io/docs/guides/terminal) - 高级PowerShell操作

### 🤝 社区与支持

- [❓ FAQ](https://sirius.publickey.io/docs/community/faq) - 常见问题
- [🐛 GitHub Issues](https://github.com/purpose168/Sirius-cn/issues) - Bug报告和功能请求
- [💬 Discord社区](https://sirius.publickey.io/community) - 实时社区支持
- [🤝 贡献指南](./documentation/contributing.md) - 如何为Sirius做出贡献
- [📧 支持联系](mailto:support@publickey.io) - 直接技术支持

## 📊 性能与扩展

### 📈 不同使用场景的系统要求

| 使用场景            | CPU       | RAM   | 存储 | 网络    |
| ------------------- | --------- | ----- | ------- | ---------- |
| **个人实验室**    | 2 核心   | 4GB   | 20GB    | 基础      |
| **小型企业**  | 4 核心   | 8GB   | 100GB   | 专用  |
| **企业级**      | 8+ 核心  | 16GB+ | 500GB+  | 高速 |
| **MSP/大规模** | 16+ 核心 | 32GB+ | 1TB+    | 企业级 |

### ⚡ 性能优化

```bash
# 监控资源使用情况
docker stats

# 为大型环境优化
# 编辑 docker-compose.yaml 并添加：
services:
  sirius-engine:
    deploy:
      resources:
        limits:
          cpus: '4.0'
          memory: 8G
        reservations:
          cpus: '2.0'
          memory: 4G
```

## 🆕 更新内容

### 最近更新

- ✅ **修复 Nmap 配置**：解决重复端口规格警告
- ✅ **增强开发模式**：改进本地开发的卷挂载
- ✅ **更好的错误处理**：增强调试和日志记录功能
- ✅ **性能改进**：优化容器启动和资源使用
- ✅ **安全增强**：更新默认配置和安全实践

### 即将推出的功能

- 🔄 **高级报告**：增强的 PDF 和仪表板报告
- 🎯 **AI 驱动分析**：自动化漏洞风险评估
- 📱 **移动支持**：移动响应式界面改进
- 🔌 **插件系统**：可扩展的扫描模块架构
- ☁️ **云集成**：原生云平台扫描支持

## 📄 许可证

本项目根据 [LICENSE](./LICENSE) 文件中指定的条款进行许可。

---

**🚀 准备开始扫描？** 按照我们的 [快速开始指南](https://sirius.publickey.io/docs/getting-started/quick-start)，在 5 分钟内让 Sirius 运行起来！

**💡 需要帮助？** 加入我们的 [Discord 社区](https://sirius.publickey.io/community) 获取实时支持和讨论。

**🐛 发现错误？** 在 [GitHub Issues](https://github.com/purpose168/Sirius-cn/issues) 上报告 - 我们会快速响应！

---

_对于生产部署，请始终更改默认凭据并查看我们的 [安全指南](https://sirius.publickey.io/docs/guides/security) 以获取最佳实践。_
