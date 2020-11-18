# 提交规范

安装插件来帮我们规范 `git commit` 提交、自动化发布版本，自动生成 `CHANGELOG`

## husky

用 `husky` 来管理 `git commit` 之前的操作，为什么要这么做，因为我们可以在 `git commit` 之前再校验一次代码，防止提交「脏」代码，保证代码库中的代码是「干净」的，`husky` 不仅仅能管理 `commit`，`git` 的钩子几乎都能管理，不过用的最多的还是 `commit` 和 `push`

```bash
$ yarn add husky -D
```

在`package.json`中配置

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint"
    }
  }
}
```

这里在 `commit` 之前，我们先执行了 `npm run lint`，会根据我们的 `eslint` 规则来校验代码，并且自动修复，记得先 `git add` 文件

但这样会有一个问题，就是这次提交，我可能只修改了一个文件，比如我就修改了 `a.js` 的内容，但它依然会校验 `src` 下面所有的 `.js` 文件，非常的不友好。
导致的问题就是：每次提交代码，无论改动多少，都会检查整个项目下的文件，当项目大了之后，检查速度也会变得越来越慢

> 注：因为 pre-commit 这个钩子是需要配合 git 去用的，它主要对文件夹 .git/hooks/pre-commit 文件做手脚，当我们从 git 上拉下来项目时，如果之前没对 hooks 下的文件做修改，那 hooks 下的文件都是以 sample 结尾的，这个时候钩子函数是不起作用的，当我们安装了 husky 之后，husky 会自动对 hooks 下面的文件做修改，当你安装完 husky 之后，再打开 .git/hooks 文件夹，你会发现所有的钩子文件，都会存在一份带有.sample 的，一份不带.sample 的，不带.sample 的文件就是 husky 创建的，这个才会让 git 钩子起作用。所以我们最好是先拉项目，然后再安装 husky，否则可能会导致 husky 失效。如果你是新开发项目，开发完后才提交到 git，开发完之后，你可以先关联 git 仓库，然后重新安装一下 husky 这个包就可以了

> 注：如果在`sourcetree`直接跳过`husky`钩子，
> 执行 commit 后可以看到对应的`shell log`提示`Can‘t find npx in path ....`
> 在路径中添加`yarn`的位置

```
$ which yarn
--> /usr/local/bin/yarn
```

在`home`目录下新建`~/.huskyrc` 文件

```
export PATH=/usr/local/bin:$PATH
```

## lint-staged

解决上面的痛点就需要使用 `lint-staged`。它只会校验你提交或者说你修改的部分内容。

```bash
yarn add lint-staged -D
```

修改`package.json` 配置

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,jsx}": ["eslint --fix", "git add"]
  }
}
```

如上配置，每次它只会在你本地 `commit` 之前，校验你提交的内容是否符合你本地配置的 `eslint` 规则，如果符合规则，则会提交成功。如果不符合它会自动执行 `eslint --fix` 尝试帮你自动修复，如果修复成功则会帮你把修复好的代码提交，如果失败，则会提示你错误，让你修好这个错误之后才能允许你提交代码。

## Commit 提交规范检查

在多人协作项目中，如果代码风格统一、代码提交信息的说明准确，在后期维护以及 `Bug` 处理时会更加方便。
`Git` 每次提交代码，都要写 [`Commit message`（提交说明）](./CommitMessage.md)

但是每个人的提交方式不同，没有很特别的指定哪些 `commit` 是新功能，哪些是修复 `bug`，这时需要插件来帮我们规范化

规范 `Commit message` 的作用

- 提供更多的历史信息，方便快速浏览
- 过滤某些 `commit`（比如文档改动），便于快速查找信息
- 直接从 `commit` 生成 `CHANGELOG`
- 可读性好，清晰，不必深入看代码即可了解当前 `commit` 的作用。
- 为 `Code Review`（代码审查）做准备
- 方便跟踪工程历史

### 第一步 替代 git commit

```bash
// [推荐全局安装] commitizen 为我们提供一些 cli 命令
// 比如：commitizen init、 git cz

$ yarn global add commitizen  -D
```

- `commitizen`: 我们需要借助它提供的 `git cz` 命令替代我们的 `git commit` 命令, 帮助我们生成符合规范的 `commit message`.

```bash

$ yarn add cz-conventional-changelog -D
```

- `cz-conventional-changelog`:为`commitizen` 指定一个 `Adapter`，使得 `commitizen` 按照我们指定的规范帮助我们生成 `commit message`.

配置`package.json`

```json
{
  "scripts": {
    "commit": "git-cz"
  },
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
}
```

**使用**
依赖安装完就可以开始秀操作了

要先 `git add .` 将文件加入本地暂存区后，才能 `commit`

```bash
// 如果全局安装 commitizen
$ git cz
// 如果安装在本地 commitizen
$ npm run commit
```

> 注意，如果之前通过 git commit 这种方式提交代码，都要改为 git-cz

**自定义 adapter**

- `cz-customizable`: 可自定义的`Commitizen`插件。比如：默认的提交 `types` 可能特别多，有些时候我们可能只需要其中的某些 `type`，或者自定义`type`

```bash
$ yarn add cz-customizable -D
```

配置 `package.json`

```json
{
  "config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    }
  }
}
```

在根目录下，配置 `.cz-config.js`

```js
module.exports = {
  types: [
    {
      value: "feat",
      name: "feat:     A new feature"
    },
    {
      value: "fix",
      name: "fix:      A bug fix"
    },
    {
      value: "docs",
      name: "docs:     Documentation only changes"
    },
    {
      value: "refactor",
      name:
        "refactor: A code change that neither fixes a bug nor adds a feature"
    },
    {
      value: "perf",
      name: "perf:     A code change that improves performance"
    },
    {
      value: "test",
      name: "test:     Add missing tests or correcting existing tests"
    },
    {
      value: "build",
      name: "build:    Add missing tests or correcting existing tests"
    },
    {
      value: "revert",
      name: "revert:   Revert to a commit"
    }
  ],
  allowBreakingChanges: ["feat", "fix", "refactor", "perf", "build", "revert"]
};
```

### 第二步：校验 commit

`commitlint` 帮我们规范 `commit message`（`commitlint`的实现方式和`commitizen`差不多也需要个 `adapter`）

- `@commitlint/cli` 【命令行工具】
- `@commitlint/config-conventional` 【校验规则】符合 Angular 团队规范（不同于代码规范），当然还有其它规范。

```bash
// [推荐局部安装]
$ yarn add @commitlint/config-conventional @commitlint/cli -D
```

在`package.json`配置

```json
{
  "commitlint": {
    "extends": ["@commitlint/config-conventional"]
  }
}
```

也可以，在根目录下使用配置文件： `.commitlintrc.js`

```js
module.exports = {
  extends: ["@commitlint/config-conventional"]
};
```

**针对自定义的 Adapter 进行 Lint**

如果是使用`cz-customizable`适配器做了破坏 `Angular` 风格的提交说明配置，那么不能使用`@commitlint/config-conventional`规则进行提交说明校验，可以使用`commitlint-config-cz` 对定制化提交说明进行校验。

```bash
yarn add commitlint-config-cz @commitlint/cli -D
```

此时的 `.commitlintrc.js` 文件：

```js
module.exports = {
  extends: ["cz"]
};
```

### 第三步: Husky

配置 `package.json`

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -e $GIT_PARAMS"
    }
  }
}
```

## standard-version

以上配置已经可以满足提交代码的常规要求，但是如果我们想自动生成 `CHANGELOG`，语义化我们的版本[（Semantic Versioning）](https://semver.org/lang/zh-CN/)。 就需要借助 `standard-version`

`standard-version`的作用就是生成 `changelog` 更新 `package.json` 和 `package.lock.json` 中的 `version` 字段。
关于版本

```
// 版本
major：主版本号，不兼容的API修改
minor：次版本号，向下兼容，功能性增加
patch：修订号，向下兼容，bug fixed

// 版本发布进度
alpha（内测）
beta（公测）
rc （正式版本的候选版本）  Release Candiate

// npm 发布指令
升级补丁版本号：npm version patch。
升级小版本号：npm version minor。
升级大版本号：npm version major。
```

关于`release:`

```
// 发布首个版本
npm run release -- --first-release

// 发布预发布版本
// 例如：v1.0.0 -> v1.0.0-0
npm run release -- --prerelease

// 发布与首个 alpha 版本
// 例如：v1.0.0 -> 1.0.1-alpha.0
npm run release -- --prerelease alpha

// 发布 major、minor、patch 版本
npm run release -- --release-as minor
```

```bash
$ yarn add standard-version -D
```

配置`package.json`

```json
{
  "script": {
    "release": "standard-version"
  }
}
```

## 参考链接

- [从 Commit 规范化到发布自定义 CHANGELOG 模版](https://juejin.im/post/6844903888072654856#heading-3)
- [优雅的提交你的 Git Commit Message](https://juejin.im/post/6844903606815064077#heading-3)
- [如何配置 Git Commit Message](https://zhuanlan.zhihu.com/p/69635847)
