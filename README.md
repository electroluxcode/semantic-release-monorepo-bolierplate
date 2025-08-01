# semantic-release-bolierplate 

一个用于使用 semantic-release 的启动模板，具有使用 semantic-release 进行自动版本控制和发布的功能。

## 特性

- 🚀 使用 semantic-release 自动发布
- 📝 自动生成更新日志
- 🔄 GitHub Actions 工作流程用于 CI/CD

## 开始使用

### 设置令牌

此模板使用 GitHub Actions 进行自动发布。您需要设置以下令牌：

#### 1. GitHub 令牌 (GH_TOKEN)

1. [创建 Github 个人访问令牌](https://github.com/settings/tokens)
2. 点击 `Generate new token`
3. 根据你的需求选择 `Repository access`
3. 生成具有以下权限的新令牌：
   - Actions - read and write
   - Commit statuses - read and write
   - Contents - read and write
   - Deployments - read and write

#### 2. NPM 令牌 (NPM_TOKEN)
1. 访问 [npmjs.com](https://www.npmjs.com/)
2. 导航到您的个人设置
3. 选择 "Access Tokens"
4. 创建新的访问令牌（注意把权限勾上）

#### 3.  Github设置环境变量

1. 仓库中 访问 settings / secrets and variables / actions / Repository secrets
2. 将刚刚的 `GH_TOKEN` 和 `NPM_TOKEN` 设置进去

#### 4. 开启 GitHub Actions

1. 参考本仓库的 `release.yml`


#### 5. releaserc.json

这是 semantic 的配置，在各个 子包的根目录放一个
```js
{
  // git仓库地址 - 指定项目的Git仓库地址
  "repositoryUrl": "git@github.com:electroluxcode/semantic-release-monorepo-bolierplate.git ",
  
  // 分支触发条件 - 指定哪些分支可以触发自动发布
  "branches": [
   "master"  // 只有master分支的提交才会触发发布
  ],
  
  // 插件配置 - 按顺序执行的插件列表
  "plugins": [
    
    // 1. 提交分析器 - 分析提交消息来确定版本发布类型
    "@semantic-release/commit-analyzer",
    
    // 2. Monorepo NPM 插件 - 处理monorepo中的包发布
    ["semantic-release-monorepo-npm-plugin", {
      "isReplaceWorkspace": true  // 替换workspace依赖为具体版本
    }],
    
    // 3. 发布说明生成器 - 根据提交生成发布说明
    "@semantic-release/release-notes-generator",
    
    // 4. 更新日志插件 - 生成和更新CHANGELOG.md文件
    [
      "@semantic-release/changelog",
      {
        "changelogFile": "CHANGELOG.md"  // 指定更新日志文件名
      }
    ],
    
    // 5. Git插件 - 提交生成的文件到Git仓库
    [
      "@semantic-release/git",
      {
        // 需要提交到Git的文件列表
        "assets": [
          "CHANGELOG.md",   // 更新日志文件
          "package.json"    // 包配置文件（包含新版本号）
        ],
        // 提交消息模板，[skip ci]避免触发新的CI构建
        "message": "chore(release): v${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
      }
    ]
  ],
  
  // 继承monorepo的基础配置
  "extends": "semantic-release-monorepo"
}


```

## 发布流程

此模板使用 semantic-release 进行自动版本控制和发布。当更改推送到主分支时，会自动触发发布流程。

提交消息应遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

- `feat: ...` - 新功能（次要版本发布）
- `fix: ...` - 错误修复（补丁版本发布）
- `BREAKING CHANGE: ...` - 破坏性更改（主要版本发布）