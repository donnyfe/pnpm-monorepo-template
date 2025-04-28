# pnpm-monorepo-template

pnpm monorepo 项目启动模板

[pnpm 文档](https://pnpm.io/zh/motivation)

## 安装 pnpm

```sh
npm install pnpm -g
```

## 创建项目

```sh
mkdir monorepo
​
cd monorepo
​
pnpm init
​
mkdir packages
```

### 添加 workspace 配置文件

在项目根目录下新建 `pnpm-workspace.yaml` 文件,

```sh
packages:
  - 'packages/*'
```

说明：将`packages`目录下所有的目录都作为单独的包进行管理。

## 依赖管理

### 安装全局依赖

```sh
pnpm install lodash -w
```

说明: 使用 `-w` 参数，告诉 pnpm 将依赖安装到 workspace-root，也就是项目的根目录。

### 安装局部依赖

```sh
# 进入目录
cd packages/charts
# 安装依赖
pnpm install echarts

# or
pnpm i dayjs -r --filter @monorepo/package1
```

参数说明：

- `-r`: 是 `--recursive` 的简写,表示递归执行命令
- `--filter`: 过滤器,用来指定要在哪个包中安装依赖

### 安装项目间依赖

在模块 package1 中安装模块 package2

```sh
pnpm add @monorepo/package2 -r --filter @monorepo/package1
```

比如 charts 模块依赖 utils 模块，为了让依赖实时更新最新版本，**使用通配符更新版本**。

```sh
# @*通配符表示默认同步最新版本
pnpm add utils@* -r --filter charts
# 如果命令在 zsh 终端下出现报错 可以用 pnpm add "utils@*" -r --filter charts 去安装
```

## 发包

- [changesets](https://github.com/changesets/changesets)

Changesets 是一个用于 monorepo 项目下版本以及 changelog 文件管理的工具。管理 monorepo 项目下子项目版本的更新、changelog 文件生成、包的发布。

### 安装 changesets

```sh
pnpm add @changesets/cli -Dw
```

### 初始化 changesets

```sh
pnpm changeset init
```

`./changeset/config.json`配置：

```js
{
 "$schema": "<https://unpkg.com/@changesets/config@2.2.0/schema.json>",
 "changelog": "@changesets/cli/changelog", //更新日志生成函数的加载地址
 "commit": true, //自动提交version的改动
 "fixed": [], //捆绑发布的包
 "linked": [], // 配置哪些包要共享版本
 "access": "public", //公开，如果你想阻止一个包被发布到npm，在包的package.json中设置private: true （可选值：restricted，不公开）
 "baseBranch": "main", //主分支名
 "updateInternalDependencies": "patch", //是否主动更新package的依赖，patch、minor
 "ignore": [] //指定不发布的包
}
```

### 生成变更集

执行命令 pnpm changeset 或 pnpm changeset add，该命令将询问一系列问题，首先是您要发布的包，然后是每个包的 semver bump 类型，然后是整个变更集的摘要。在最后一步，它将显示它将生成的变更集，并确认您要添加它。

```sh
pnpm changeset
# 或者
pnpm changeset add
```

### 变更版本

```sh
changeset version
```

### 发布

```sh
pnpm changeset publish
```

### 发布测试版本

假设现在我们要发布一个测试的版本来看下功能是否正常 work，我们可以使用 changeset 的 Prereleases 功能。

通过执行 `pnpm changeset pre enter <tag>` 命令进入先进入 pre 模式。

```sh
# step1
pnpm changeset pre enter alpha   # 发布 alpha 版本
pnpm changeset pre enter beta    # 发布 beta 版本
pnpm changeset pre enter rc      # 发布 rc 版本

# step2
pnpm changeset version

# step3
pnpm run build && pnpm changeset publish

# step4
pnpm changeset pre exit
```
