# Monorepo

## 什么是 Monorepo 策略？

Monorepo 是一种将多个项目代码存储在一个仓库里的软件开发策略（"mono" 来源于希腊语 μόνος 意味单个的，而 "repo"，显而易见地，是 repository 的缩写）。将不同项目的代码放在同一个代码仓库中，这种「把鸡蛋放在同一个篮子里」的做法可能乍看之下有些奇怪，但实际上，这种代码管理方式有很多好处，无论是世界一流的互联网企业 Google，Facebook，还是社区知名的开源项目团队 Babel （如下图）都使用了 Monorepo 策略管理他们的代码。

## Monorepo 方案的优势
* 共享代码和可见性：由于所有的项目代码都集中于一个代码仓库，将很容易抽离出各个项目共用的业务组件或工具，并通过 Lerna 或其他工具进行代码内引用，代码重用将变得非常容易
* 原子性的更改： 由于项目之间的引用路径内化在同一个仓库之中，当某个项目的代码修改后，很容易追踪到会影响到其他哪些项目。通过使用工具，将很容易地做到版本依赖管理和版本号自动升级，依赖管理将变得非常简单

## Monorepo 方案的劣势
* 项目粒度的权限管理变得非常复杂：无论是 Git 还是其他 VCS 系统，在支持 Monorepo 策略中项目粒度的权限管理上都没有令人满意的方案，这意味着 A 部门的 a 项目若是不想被 B 部门的开发者看到就很难了
* 学习成本变高：不同于一个项目一个代码仓库这种模式下，组织新人只要熟悉特定代码仓库下的代码逻辑，在 Monorepo 策略下，新人可能不得不花更多精力来理清各个代码仓库之间的相互逻辑，当然这个成本可以通过新人文档的方式来解决，但维护文档的新鲜又需要消耗额外的人力
* 对于公司级别的 Monorepo 策略而言，需要专门的 VFS 系统，自动重构工具的支持：设想一下 Google 这样的企业是如何将十亿行的代码存储在一个仓库之中的？开发人员每次拉取代码需要等待多久？各个项目代码之间又如何实现权限管理，敏捷发布？任何简单的策略乘以足够的规模量级都会产生一个奇迹（不管是好是坏），对于中小企业而言，如果没有像 Google，Facebook 这样雄厚的人力资源，把所有项目代码放在同一个仓库里这个美好的愿望就只能是个空中楼阁

## 复用 packages：workspace

使用 monorepo 策略后，收益最大的两点是：
1. 避免重复安装包，因此减少了磁盘空间的占用，并降低了构建时间
2. 内部代码可以彼此相互引用

这两项好处全部都可以通过成熟的包管理工具来完成，对前端开发而言，即是 yarn（1.0 以上）或 npm（7.0 以上）通过名为 workspaces 的特性实现；推荐使用 pnpm。

为了实现前面提到的两点收益，您需要在代码中做三件事：
1. 调整目录结构，将相互关联的项目放置在同一个目录，推荐命名为 packages
2. 在项目根目录里的 package.json 文件中，设置 workspaces 属性，属性值为之前创建的目录
3. 在 package.json 文件中，设置 private 属性为 true（为了避免我们误操作将仓库发布）

通过 Monorepo 策略组织代码，您代码仓库的目录结构看起来会是这样：
```
├── package.json
└── packages/ // 这里将存放所有子 repo 目录
    ├── @mono/project-1/
    │   ├── index.js
    │   └── package.json
    ├── @mono/project-2/
    │   ├── index.js
    │   └── package.json
    ...
```

> 注意：对于子项目，统一以 @<项目名>/<子项目名>  的方式命名，这是一种社区最佳实践，不仅可以让用户更容易了解整个应用的架构，也方便您在项目中更快捷的找到所需的子项目。

## 统一配置：合并同类项 - Eslint，Typescript 与 Babel

编写代码要遵循 DRY 原则（Don't Repeat Yourself 的缩写）。理所当然地，我们应该尽量避免在多个子项目中放置重复的 eslintrc，tsconfig 等配置文件。幸运的是，Babel，Eslint 和 Typescript 都提供了相应的功能让我们减少自我重复。

### TypeScript
可以在 packages 目录中放置 tsconfig.settting.json 文件，并在文件中定义通用的 ts 配置，然后，在每个子项目中，可以通过 extends 属性，引入通用配置，并设置 compilerOptions.composite 的值为 true，理想情况下，子项目中的 tsconfig 文件应该仅包含下述内容：
```
{
  "extends": "../tsconfig.setting.json", // 继承 packages 目录下通用配置
  "compilerOptions": {
    "composite": true, // 用于帮助 TypeScript 快速确定引用工程的输出文件位置
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"]
}
```

### Eslint
对于 Eslint 配置文件，我们也可以如法炮制，这样定义子项目的 .eslintrc 文件内容：
```
{
  "extends": "../../.eslintrc", // 注意这里的不同
  "parserOptions": {
    "project": "tsconfig.json"
  }
}
```
注意到了吗，对于通用的 eslint 配置，我们并没有将其放置在 packages 目录中，而是放在整个项目的根目录下，这样做是因为一些编辑器插件只会在项目根目录寻找 .eslintrc 文件，因此为了我们的项目能够保持良好的「开发环境一致性」，请务必将通用配置文件放置在项目的根目录中。

### Babel
Babel 配置文件合并的方式与 TypeScript 如出一辙，甚至更加简单，我们只需在子项目中的 .babelrc 文件中这样声明即可：
```
{
  "extends": "../.babelrc"
}
```

当一切准备就绪后，我们的项目目录应该大致呈如下所示的结构：
```
├── package.json
├── .eslintrc
└── packages/
    │   ├── tsconfig.settings.json
    │   ├── .babelrc
    ├── @mono/project-1/
    │   ├── index.js
    │   ├── .eslintrc
    │   ├── .babelrc
    │   ├── tsconfig.json
    │   └── package.json
    └───@mono/project-2/
        ├── index.js
        ├── .eslintrc
        ├── .babelrc
        ├── tsconfig.json
        └── package.json
```

## 统一命令脚本：scripty

如果子项目足够多，可能会发现，每个 package.json 文件中的 scripts 属性都大同小异，并且一些 scripts 充斥着各种 Linux 语法，例如管道操作符，重定向或目录生成。重复带来低效，复杂则使人难以理解，这都是需要我们解决的问题。

这里给出的解决方案是，使用 scripty 管理您的脚本命令，简单来说，scripty 允许您将脚本命令定义在文件中，并在 package.json 文件中直接通过文件名来引用。这使我们可以实现如下目的：
1. 子项目间复用脚本命令
2. 像写代码一样编写脚本命令，无论它有多复杂，而在调用时，像调用函数一样调用

通过使用 scripty 管理我们的 monorepo 应用，目录结构看起来将会是这样：
```
├── package.json
├── .eslintrc
├── scirpts/ // 这里存放所有的脚本
│   │   ├── packages/ // 包级别脚本
│   │   │   ├── build.sh
│   │   │   └── test.sh
│   └───└── workspaces/ // 全局脚本
│           ├── build.sh
│           └── test.sh
└── packages/
    │   ├── tsconfig.settings.json
    │   ├── .babelrc
    ├── @mono/project-1/
    │   ├── index.js
    │   ├── .eslintrc
    │   ├── .babelrc
    │   ├── tsconfig.json
    │   └── package.json
    └── @mono/project-2/
        ├── index.js
        ├── .eslintrc
        ├── .babelrc
        ├── tsconfig.json
        └── package.json
```

我们脚本分为两类「package 级别」与「workspace 级别」，并且分别放在两个文件夹内。这样做的好处在于，我们既可以在项目根目录执行全局脚本，也可以针对单个项目执行特定的脚本。

通过使用 scripty，子项目的 package.json 文件中的 scripts 属性将变得非常精简：
```
{
  ...
  "scripts": {
    "test": "scripty",
    "lint": "scripty",
    "build": "scripty"
  },
  "scripty": {
    "path": "../../scripts/packages" // 注意这里我们指定了 scripty 的路径
  },
  ...
}
```

> 别忘了使用 chmod -R u+x scripts 命令使所有的 shell 脚本具备可执行权限，也千万别忘了把这条贴士写在您的 README.md 文件中！
