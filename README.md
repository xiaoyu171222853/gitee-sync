# Gitee to GitHub 同步工具

这是一个用于自动同步 Gitee 仓库到 GitHub 的工具，基于 GitHub Actions 实现。支持多仓库同步、分支指定、仓库可见性控制等功能。

## 功能特点
- 🔄 支持多个仓库同步
- 🕒 可配置的同步时间（默认东八区 14:00, 22:00, 23:00, 24:00）
- 🌿 支持指定源分支和目标分支
- 🔐 支持设置 GitHub 仓库的可见性（公开/私有）

## 使用方法

首先将**本项目fork到自己的仓库中**，然后按照以下步骤设置即可。

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

- `GH_TOKEN`: GitHub Personal Access Token，需要有创建和管理仓库的权限(获取token路径：Settings -> Developer Settings -> Personal access tokens)
- `GITEE_USERNAME`: Gitee 用户名
- `GITEE_PASSWORD`: Gitee 密码或访问令牌

### 3. 同步时间

工作流默认在以下时间点（东八区）运行：
- 14:00 - 检查上午的更新
- 22:00 - 检查下午的更新
- 23:00 - 第一次晚间同步
- 24:00 - 最后一次同步

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
