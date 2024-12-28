# Gitee to GitHub 同步工具

这是一个用于自动同步 Gitee 仓库到 GitHub 的工具，基于 GitHub Actions 实现。支持多仓库同步、分支指定、仓库可见性控制等功能。

## 功能特点

- 🔄 支持多个仓库同步
- 🕒 可配置的同步时间（默认东八区 14:00, 22:00, 23:00, 24:00）
- 🌿 支持指定源分支和目标分支
- 🔐 支持设置 GitHub 仓库的可见性（公开/私有）
- 🚫 自动排除 `.git` 和 `.github` 目录
- 🔑 安全的凭证管理

## 使用方法

### 1. 配置文件设置

在项目根目录创建 `sync-config.yml` 文件：

#### 配置项说明

1. **name** (必填)
   - 同步任务的唯一标识名称
   - 建议使用有意义的名称，方便在日志中识别

2. **gitee** (必填)
   - `repo_url`: Gitee 仓库的完整 HTTPS 地址
   - `branch`: 要同步的源分支名称

3. **github** (必填)
   - `repo_name`: 目标 GitHub 仓库名称
     - 如果仓库不存在，将自动创建
     - 如果仓库已存在，将同步到该仓库
   - `branch`: 目标分支名称（可选）
     - 如果不指定，将使用与 Gitee 相同的分支名
     - 可以与源分支名称不同
   - `private`: 仓库可见性设置
     - `true`: 创建/设置为私有仓库
     - `false`: 创建/设置为公开仓库
     - 此设置也会影响现有仓库的可见性

#### 注意事项

1. 确保 Gitee 仓库地址正确且可访问
2. GitHub 仓库名称不要与现有的其他仓库冲突
3. 分支名称区分大小写
4. 可以根据需要添加任意数量的仓库配置
5. 修改配置文件后无需重启，下次同步时会自动使用新配置

### 2. 设置 GitHub Secrets

在 GitHub 仓库的 Settings -> Secrets and variables -> Actions 中添加以下 secrets：

- `GH_TOKEN`: GitHub Personal Access Token，需要有创建和管理仓库的权限
- `GITEE_USERNAME`: Gitee 用户名
- `GITEE_PASSWORD`: Gitee 密码或访问令牌

### 3. 同步时间

工作流默认在以下时间点（东八区）运行：
- 14:00 - 检查上午的更新
- 22:00 - 检查下午的更新
- 23:00 - 第一次晚间同步
- 24:00 - 最后一次同步

也可以通过手动触发立即同步。

### 4. 手动触发同步

1. 进入仓库的 Actions 页面
2. 选择 "Sync from Gitee to GitHub" 工作流
3. 点击 "Run workflow" 按钮

## 注意事项

1. 确保 GitHub Personal Access Token 具有足够的权限
2. Gitee 仓库需要可访问（公开或有访问权限）
3. 同步操作会强制覆盖 GitHub 仓库的目标分支
4. 每个月有 2000 分钟的免费 Actions 时长
5. 配置文件中的仓库数量会影响执行时间

## 常见问题

1. **同步失败怎么办？**
   - 检查 Secrets 是否正确配置
   - 确认 Gitee 仓库地址和分支是否正确
   - 查看 Actions 日志了解详细错误信息

2. **如何修改同步频率？**
   - 修改 `.github/workflows/gitee-sync.yml` 文件中的 cron 表达式

3. **如何添加新的同步仓库？**
   - 在 `sync-config.yml` 中添加新的仓库配置项

## 贡献指南

欢迎提交 Issue 和 Pull Request 来改进这个工具。

## 许可证

[MIT License](LICENSE)

## 项目结构

### 核心文件说明

1. **.github/workflows/gitee-sync.yml**
   - GitHub Actions 的工作流配置文件
   - 定义了自动同步的触发条件和执行步骤
   - 包含了同步逻辑的具体实现
   - 主要功能：
     - 读取配置文件
     - 创建/更新 GitHub 仓库
     - 克隆 Gitee 仓库
     - 同步代码到 GitHub
     - 处理多仓库并行同步

2. **sync-config.yml**
   - 用户配置文件，需要根据 example 文件自行创建
   - 定义需要同步的仓库信息
   - 包含：
     - Gitee 源仓库信息
     - GitHub 目标仓库设置
     - 分支配置
     - 可见性设置

3. **sync-config.yml.example**
   - 配置文件的示例和模板
   - 包含了不同场景的配置示例
   - 提供了配置参考

### 实现流程

1. **配置解析**
   - 读取 `sync-config.yml` 获取同步任务列表
   - 使用 `yq` 工具解析 YAML 配置

2. **任务分发**
   - 使用 GitHub Actions 的 matrix 策略
   - 并行处理多个仓库的同步任务

3. **仓库处理**
   - 检查 GitHub 仓库是否存在
   - 自动创建不存在的仓库
   - 更新仓库可见性设置

4. **代码同步**
   - 克隆指定分支的 Gitee 仓库
   - 排除 `.git` 和 `.github` 目录
   - 推送代码到 GitHub 指定分支

5. **错误处理**
   - 单个仓库同步失败不影响其他仓库
   - 详细的错误日志输出
   - 失败重试机制

### 自动化运行

- 按照预设时间自动运行（东八区）：
  - 14:00
  - 22:00
  - 23:00
  - 24:00
- 支持手动触发运行
- 使用 GitHub Actions 的调度系统