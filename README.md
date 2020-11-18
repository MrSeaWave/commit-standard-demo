# 提交规范

安装插件来帮我们规范 `git commit` 提交、自动化发布版本，自动生成 `CHANGELOG`

## husky

用 `husky` 来管理 `git commit` 之前的操作，为什么要这么做，因为我们可以在 `git commit` 之前再校验一次代码，防止提交「脏」代码，保证代码库中的代码是「干净」的，`husky` 不仅仅能管理 `commit`，`git` 的钩子几乎都能管理，不过用的最多的还是 `commit` 和 `push`

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

## lint-staged

解决上面的痛点就需要使用 lint-staged。它只会校验你提交或者说你修改的部分内容。

修改`package.json` 配置
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,jsx}": [
      "eslint --fix",
      "git add"
    ]
  }
}
```
如上配置，每次它只会在你本地 `commit` 之前，校验你提交的内容是否符合你本地配置的 `eslint` 规则，如果符合规则，则会提交成功。如果不符合它会自动执行 `eslint --fix` 尝试帮你自动修复，如果修复成功则会帮你把修复好的代码提交，如果失败，则会提示你错误，让你修好这个错误之后才能允许你提交代码。
