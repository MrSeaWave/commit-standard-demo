# Commit message 格式说明

Commit message 一般包括三部分：**Header**、**Body** 和 **Footer**

**Header** `type(scope):subject` type(必需)、scope(可选) 和 subject(必需)

这里有几种类型可以选择

type：用于说明 commit 的类别，规定为如下几种

```
feat：新功能
fix：修补 bug
docs：修改文档，比如 README, CHANGELOG, CONTRIBUTE 等等
style： 不改变代码逻辑 (仅仅修改了空格、格式缩进、逗号等等)
refactor：重构（既不修复错误也不添加功能）
perf: 优化相关，比如提升性能、体验
test：增加测试，包括单元测试、集成测试等
build: 构建系统或外部依赖项的更改
ci：自动化流程配置或脚本修改
chore: 非 src 和 test 的修改
revert: 恢复先前的提交
```

scope：(可选)用于说明 commit 影响的范围

subject：commit 的简要说明，尽量简短

**Body**

Body 部分是对本次 commit 的详细描述，可以分成多行

**Footer**

Footer 部分只用于两种情况。

- 不兼容变动

如果当前代码与上一个版本不兼容，则 Footer 部分以 **BREAKING CHANGE** 开头，后面是对变动的描述、以及变动理由和迁移方法。

- 关闭 Issue

如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue, 也可以一次关闭多个 issue

```
? Select the **type** of change that you're committing:
(type) 选择提交更改的类型
? What is the **scope** of this change (e.g. component or file name)? (press enter to skip)
(scope) 此次更改的范围是什么（组件或者文件名）
? Write a **short**, imperative tense description of the change:
(subject) 写一个简短的，命令式的变化描述
? Provide a **longer description** of the change: (press enter to skip)
(Body) 提供更改的长描述
? Are there any **breaking changes**?
(Footer) 有没有突破性的变化
? Does this change affect any open **issues**? (y/N)
(Footer) 此次更改是否有要关闭 issues
复制代码
```

如果当前 commit 针对某个 issues

```
? Does this change affect any open issues? (y/N)
```

选择 Y，输入 **Closes #1** (表示关闭第 1 个 issues)

也可以一次关闭多个 issues ： Closes #1 #2 #3

![image](https://user-images.githubusercontent.com/21967852/99515349-cd07c000-29c7-11eb-96c2-913acdca8d55.png)

CHANGELOG 中 issues 默认的链接地址是根据 package.json 中的 **repository** 来生成的

如果 `repository` 没有，则会获取 git 中的**远程仓库路径**来作为前缀

之后就会进行**代码格式化校验**，如果代码不符合规范，同样会提交失败，**一定要确保项目当前格式没问题，规范后再提交！！**

## 参考链接

- [Commit message 和 Change log 编写指南](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
