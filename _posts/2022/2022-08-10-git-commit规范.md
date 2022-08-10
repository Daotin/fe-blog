---
title: git commit 提交规范
tags: 代码规范
---

# Git Commit提交规范

![Untitled](./images/commit-0.png)

## TLDR（太长不看版）
已经了解了或者想快速完成项目配置的，可以按照下面操作快速完成**推荐**的配置：

### 1、安装

- 必须安装

```
# 1、安装husky
npm install husky -D

# 2、配置husky
npx husky-init && npm install

# 3、去.husky/pre-commit中删除 npm test

# 4、添加commit-msg hook（如果失败，需要升级npm）
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'

# 5、安装commitlint
npm install --save-dev @commitlint/config-conventional @commitlint/cli

# 6、在工程目录下创建 commitlint.config.js，内容如下：
module.exports = {
  extends: ['@commitlint/config-conventional'],
};

# 7、安装自动生成changelog
npm install conventional-changelog-cli -g
npm install conventional-changelog-cli -D

#8、执行下面命令，即可生成angular提交格式的changelog
conventional-changelog -p angular -i CHANGELOG.md -s
```

- 可选安装（*如果需要使用commitizen命令行提交commit的话*）

```
# 1、安装commitizen
npm install -g commitizen

# 2、安装适配器
commitizen init cz-conventional-changelog --save-dev --save-exact

# 3、git add后，输入 cz 即可使用
```

### 2、vscode安装插件（二选一）
- [Commit Message Editor](https://marketplace.visualstudio.com/items?itemName=adam-bender.commit-message-editor)
- [Conventional Commits](https://marketplace.visualstudio.com/items?itemName=vivaxy.vscode-conventional-commits)

（完）

---

## 为什么需要制定git commit提交规范

现在的项目大多是团队开发，由于每个人都有自己的编码习惯，其中为了提高代码质量和开发效率，有了 Eslint 和 Prettier 工具，帮助我们统一代码规范。

但是，一直以来，commit message 却总是被忽视。在日常开发中，大家的 commit message 千奇百怪，中英文混合使用、fix bug，优化代码等各种笼统的信息司空见怪，这就导致后续在回顾提交记录的时候特别混乱，有时自己都不知道自己的 fix bug 修改的是什么问题，需要看提交代码才知道。

一份良好的commit记录的好处如下：

- 首行就是简洁实用的关键信息，方便在 git 历史中快速浏览
- 具有更加详细的 body 和 footer，可以清晰的看出某次提交的目的和影响
- 通过type可以快速过滤某些commit（比如文档改动）
- 通过commit可以自动生成Change log

## 业界通用的提交规范有哪些？

1. [Angular commit convention](https://zj-git-guide.readthedocs.io/zh_CN/latest/message/Angular%E6%8F%90%E4%BA%A4%E4%BF%A1%E6%81%AF%E8%A7%84%E8%8C%83/)：Angular项目的提交规范
2. [Conventional Commits](https://www.conventionalcommits.org/zh-hans/v1.0.0/)：约定式提交规范，基于Angular提交规范，提供了更加通用、简洁和灵活的提交规范。

示例如下：（可以看出这些提交信息都是有固定格式的）

![Untitled](/images/20220810/commit-0.png)

### 标准格式

每个提交消息都由一个头部 header、一个正文 body 和一个页脚 footer 组成。

#### 头部header部分

header具有特殊格式，包括 `type`, `scope` 和 `subject`:

```jsx
<type>[optional scope][!]: <subject>
# 空行
[optional body]
# 空行
[optional footer]
```

`type`**（必填）**：**说明此次提交的类型。**比如feat、fix，后跟`冒号(英文)和一个空格`

type需要指定为下面其中一个：

- `feat`：添加新功能（比如新增一个按钮操作）。
- `fix`：修复bug。
- `docs`：文档修改（添加或者更新文档）。
- `style`：不影响代码功能的变动，**不包括样式修改**（比如去掉空格、改变缩进、增删分号等）。
- `refactor`：代码重构（即不是新增功能，也不是修改bug的代码变动，比如变量重命名）。
- `perf`：代码优化（比如提升性能、体验）
- `test`：添加或者更新测试。
- `build`：影响构建打包，或外部依赖的修改，比如发布版本，对项目依赖的改动等
- `ci`：脚本变更（对CI配置文件或者脚本的修改）
- `chore`：其他不会修改src或者test的修改
- `revert`：恢复到上一个版本。

> 问题：没有单独修改UI的type。
> 
> 
> 解决方案：
> 
> 1. UI的修改归类在style中
> 2. 新增一个UI类型



`scope`（可选）：**表明本次提交影响的范围**。用**英文圆括号**包围。

比如可以取值 api，表明只影响了接口。



`!`（可选）：表示破坏性变更。（跟下面的`BREAKING CHANGE` 相同，可以理解成重大修改，类似版本升级、接口参数减少、接口删除等。如果使用了 `!`，那么脚注中**可以**不写 `BREAKING CHANGE`）



`subject`**（必填）**：本次提交的简短描述概括。

（建议不要过长，如果感觉太短说不清楚，可以在body中补充。）



#### 正文body部分

`body`（可选）：比较详细描述本次提交涉及的条目，罗列代码功能，不是必须的。

- 位置：正文**必须**起始于描述subject字段结束的一个空行后。
- 正文内容自由编写，并**可以**使用空行分隔不同段落。



#### 页脚footer部分

`footer`（可选）：一般包括破坏性变更(BREAKING CHANGE) 或 解决关闭的 issue 。

- 位置：在正文body结束的一个空行之后，**可以**编写一行或多行脚注。脚注的值**可以**包含空格和换行，值的解析过程**必须**直到下一个脚注的令牌/分隔符出现为止。（也就是**可以**使用空行分隔脚注）
- 每行脚注都**必须**包含一个令牌（token），后面紧跟 `:<space>` 作为分隔符，后面再紧跟令牌的值（比如：`Fix: #111`）
- 如果令牌是多个单词组成，令牌**必须**使用 `-`作为连字符，比如 `Acked-by`(这样有助于 区分脚注和多行正文)。有一种例外情况就是 `BREAKING CHANGE`，它**可以**被认为是一个令牌。
- 破坏性变更可以作为**脚注的一项**。一个破坏性变更**必须**包含大写的文本 `BREAKING CHANGE`，后面紧跟**冒号和空格。**



### 示例

示例1：最小描述

```
feat: 增加搜索功能
```

示例2：加了影响范围和详细描述正文

```
feat(notice): 增加搜索功能

1、在通知页面增加搜索功能
2、搜索范围在一个月以内
```

示例：加入!（破坏性变更）

```
build!: 增加md5库，需要重新npm imstall
```

示例3：加了footer

```
feat: 增加搜索功能

Closes: http://10.8.40.130:8081/browse/QJYH-28
```

示例4：加了BREAKING CHANGE

```
build: 增加md5库

BREAKING CHANGE: 需要重新 npm install
```

示例5：最全的

```
feat(notice): 增加搜索功能

功能描述：
1、在通知页面增加搜索功能
2、搜索范围在一个月以内

需要注意：接口超时提醒。

BREAKING CHANGE: 需要重新 npm install

Reviewed-by: XXX
Closes: #111, #222, #333
```

## 如何快速生成符合规范的commit信息？

虽然有了规范，但是如果要每次提交都需要自己手写的话还是比较麻烦的，而且还是无法保证每个人都能够遵守相应的规范，因此就需要使用一些工具来保证大家都能够提交符合规范的Commit Message。

常用的工具包括了**可视化工具**和**命令行工具**，其中`Commitizen`是常用的命令行工具，接下来将会先介绍Commitizen的使用方法。

### Commitizen

#### 什么是Commitizen

[Commitizen](https://github.com/commitizen/cz-cli)是一个撰写符合上面Commit Message标准的一款工具，可以帮助开发者提交符合规范的Commit Message。

#### 安装Commitizen

**1、全局安装commitizen命令行**

```
npm install -g commitizen
```



**2、安装适配器。**

`Commitizen` 支持多种不同的提交规范，可以安装和配置不同的适配器实现。因为我们使用约定式提交规范，所以使用 `cz-conventional-changelog` 适配器初始化项目

```
commitizen init cz-conventional-changelog --save-dev --save-exact
```

它会在package.json中添加下面内容

```jsx
"devDependencies": {
	"cz-conventional-changelog": "^3.3.0"
},
"config": {
	"commitizen": {
		"path": "./node_modules/cz-conventional-changelog"
	}
}
```

或者，可以将 Commitizen 配置添加到一个 `.czrc` 文件中：

```jsx
{
  "path": "cz-conventional-changelog"
}
```



**3、生成标准commit信息**

在命令行输入`cz`，或者`git cz` ，系统会提示您填写一些必填字段，并且您的提交消息进行格式化。

![Untitled](/images/20220810/commit-1.png)

它会引导你一步步填下去，然后自动提交该commit。

![Untitled](/images/20220810/commit-2.png)

> 遇到的问题：
>
> 在输入cz的是提示，cz-conventional-changelog 找不到，看了路径是默认找的.git下面的node_modules，因为我们项目是项目集的形式，所以要修改路径。正常的单个项目不需要修改。

![Untitled](/images/20220810/commit-3.png)

```jsx
"config": {
	"commitizen": {
		"path": "cz-conventional-changelog"
	}
}
```



**4、自定义适配器**

从上面的截图可以看到，`git cz`终端操作提示都是英文的，如果想改成中文的或者自定义这些配置选项，我们使用 `cz-customizable`*适配器。

具体的配置可以参考：

- 官网：[https://github.com/leoforfree/cz-customizable](https://github.com/leoforfree/cz-customizable)
- [https://juejin.cn/post/6951649464637636622#heading-32](https://juejin.cn/post/6951649464637636622#heading-32)
- [https://juejin.cn/post/7005135785263366157](https://juejin.cn/post/7005135785263366157)

最后可以配置成中文的，甚至可以跳过其中的某些步骤：

![Untitled](/images/20220810/commit-4.png)



### 可视化工具

除了使用Commitizen命令行工具来帮助我们规范Commit Message之外，我们也可以使用编译器自带的可视化提交工具。接下来，将会分别介绍 VSCode 和 Webstorm 可视化提交工具的使用方法。

#### vscode

插件一：Commit Message Editor

![Untitled](/images/20220810/commit-5.png)

![Untitled](/images/20220810/commit-6.png)

![Untitled](/images/20220810/commit-7.png)



插件二：Conventional Commits

![Untitled](/images/20220810/commit-8.png)

![Untitled](/images/20220810/commit-9.png)

缺点：没有对`BREAKING CHANGE` 的选择提示，需要自己手写。

#### webstorm

![Untitled](/images/20220810/commit-10.png)

![Untitled](/images/20220810/commit-11.png)

## 如何校验commit message

虽然我们有命令行和可视化交互工具帮助大家规范自己的 commit message，但是依然没法保证大家都按照规范提交commit message，因为即使是不规范的commit message也是依然可以提交成功的。

所以，有必要在提交的时候校验commit message是否按照规范填写的，如果不规范，则阻止提交。

commit message的校验在两个地方都可以进行：

- 一是在`客户端`，也就是在我们提交代码的时候进行校验；
- 二是在push到`服务器`的时候，由服务器端进行校验。

在此之前，先介绍下git常用的钩子 Git Hook。



### Git Hook

hook，直译过来是“钩子”，通常是用于在某事件发生或者完成后添加自定义的动态事件/任务。在使用 git 时，git 的一些特定的重要动作（比如git commit，git push等）也会触发一些特定的事件，通过这些事件，我们可以完成一些自动测试、集成、构建等流程工作。

git hook有很多，包括客户端和服务端两种。



#### 常见服务端git commit钩子

- `pre-receive`：当客户端push的时候，该钩子最先被调用。可以对push的内容做校验，如果校验脚本正常退出（exit 0）即允许push，反之会拒绝客户端进行push操作。
- `update`：update钩子在pre-receive之后被调用，用法也差不多。它也是在实际更新前被调用的，但它可以分别被每个推送上来的引用分别调用。也就是说如果用户尝试推送到4个分支，update会被执行4次。和pre-receive不一样，这个钩子不需要读取标准输入。
- `post-receive`：在成功push后被调用，适合用于发送通知。类似于客户端的post-commit。



**操作步骤**

> *（PS：以下内容，因没有相关资源暂无法实践）*

1、对于Gitlab来说，对于本地部署的Gitlab服务器，需要 GitLab 管理员在 GitLab 服务器的**文件系统**上配置服务器钩子，可以通过 [custom_hooks](https://docs.gitlab.com/ee/administration/server_hooks.html) 配置：

假如我们要向名为 **project** 的仓库添加 git hooks 脚本：

- 找到 server 端project仓库的根目录

  - 从源安装的，路径通常为 `/home/git/repositories/<group>/<project>.git`

  - 通过 `Omnibus` 安装，路径通常为 `/var/opt/gitlab/git-data/repositories/<group>/<project>.git`

- 在该路径下创建 `custom_hooks` 文件夹

- 在 `custom_hooks` 文件下创建 git hook 文件，如 `pre-receive` 等（没有后缀）

- 为 git hook 脚本添加可执行权限，如 `chmod +x pre-receive`



hook文件示例：

```
#!/bin/sh

echo "Say hi from gitlab server ok 😄"
exit 0

```

参考文档：

- [gitlab配置custom hook](https://java.isture.com/tools/gitlab/custom-hook.html#gitlab%E9%85%8D%E7%BD%AEcustom-hook)
- [pre-receive-hooks 写法示例](https://github.com/github/platform-samples/tree/master/pre-receive-hooks)



2、如果您没有文件系统访问权限，可以使用的是 [Push Rules](https://docs.gitlab.com/ee/user/project/repository/push_rules.html)，用于用户可配置的 Git 钩子接口。（**该方法适用于付费版 gitlab 用户**）。

有两种配置方式：针对全部项目的和针对单独项目的。

针对全部项目：

1. 在顶部栏上，选择**“菜单”>管理员**”。
2. 在左侧边栏上，选择**推送规则Push Rules**。
3. 展开**“推送规则Push Rules**”。
4. 设置所需的规则。
5. 选择**保存推送规则**。

针对单个项目的推送规则：（单个项目的推送规则将覆盖全局推送规则。）

1. 在顶部栏上，选择**“菜单>项目”**，然后找到你的项目。
2. 在左侧边栏上，选择**“设置”>存储库**”。
3. 展开**“推送规则**”。
4. 设置所需的规则。
5. 选择**保存推送规则**。



针对单个项目，如果开启了 Push Rules，则在`仓库 --> 设置 --> Push Rules` 中有 commit message，可以使用正则匹配，匹配成功才可以提交。

例如，如果每个提交都应该引用 Jira 问题（如 Fix: JIRA-123），则正则表达式将为 `JIRA\-\d+`。

![](/images/20220810/commit-18.png)

然后在push的时候，如果不符合规范就会报错：

```
remote: GitLab: Commit message does not follow the pattern '^(fix|feat|refactor|chore|style|docs|test):([\s\S]){5,}$|^Merge|^Resolve|^Feat'
...
master -> master (pre-receive hook declined)
```



**PS：对于github企业版，也可以设置服务端hook。**

步骤可以参考：https://docs.github.com/cn/enterprise-server@3.6/admin/policies/enforcing-policy-with-pre-receive-hooks/managing-pre-receive-hooks-on-the-github-enterprise-server-appliance

其中需要创建的具体hook脚本参考示例：https://github.com/github/platform-samples/tree/master/pre-receive-hooks



#### 常见客户端git commit钩子

- `pre-commit` ：在提交 commit message 之前运行。

一般用来做一些代码提交前的准备工作。比如判断提交的代码是否符合规范、是否对这份提交进行了测试、代码风格是否符合团队要求等等。

- `prepare-commit-msg` ：在 commit message 提交后，commit message 被保存前运行。

这个钩子用的机会不是太多，主要是用于能自动生成commit message，合并压缩提交等场合。

- `commit-msg`

包含有一个参数，用来指定提交message规范文件的路径。

该脚本可以用来验证提交的message是否规范，如果作者写的提交message不符合指定路径文件中的规范，提交就会被终止。

- `post-commit` ：在整个提交过程完成之后运行。这个脚本不包含任何参数，也不会影响commit的运行结果，可以用于发送提交通知。



>  客户端的hook，由于没有平台的限制，且配置方便，因而更加常用。我们需要在`.git/hooks`文件夹下修改相关的hook文件，但是.git文件夹下的文件不能同步到项目仓库中，因而需要每个开发者在自己本地手动配置，比较麻烦，而且写法可能也不熟悉。
>  所以我们需要一个工具方便我们配置客户端hook，并且可以同步到代码仓库，它就是Husky。
>
>  ![](/images/20220810/commit-19.png)



### Husky

[Husky](https://typicode.github.io/husky/#/) 是一个开源 Git Hook工具，它可以让开发人员更加便捷快速使用 Git Hook。



**1、安装husky**

```
npm install husky -D
```



**2、配置 husky**

自动配置（推荐）：使用 `husky-init`命令快速在项目初始化一个 husky 配置。

```
npx husky-init && npm install
```

它将设置 husky，修改 package.json 并创建一个您可以编辑的示例 pre-commit 钩子。默认情况下，它会在你提交时运行 `npm test`。

> 错误提示：还是因为我们是项目集的形式，每个项目下没有`.git`文件夹。所以我们需要在仓库根目录下，执行该命令。（可以参考最后的[补充说明](#补充说明)）
> 

![Untitled](/images/20220810/commit-12.png)

> 注意事项：由于它默认创建的pre-commit中会执行npm test指令，但是我们项目中没有对应的指令，所以后续在commit的时候会报错，所以需要在生成的pre-commit文件中，把npm test指令删除。



**3、添加其他hook（比如commit-msg hook）**

```jsx
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

错误提示：

![Untitled](/images/20220810/commit-13.png)

解决方案：**升级npm**

![Untitled](/images/20220810/commit-14.png)



### commitlint

[commitlint](https://commitlint.js.org/#/) 是一个用来检测 commit message 的开源工具，我们可以用来检查您的提交消息是否符合 Conventional Commits（约定式提交规范）。配合[husky](https://github.com/typicode/husky)，我们可以在提交commit的时候进行校验和拦截操作。

> 执行过程：具体的过程就是，在提交message的时候，通过 Git Hook工具 Husky，执行 commitlint 校验，如果不通过，就阻止message提交。



**1、安装**

```
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

**2、在工程目录下创建`commitlint.config.js`，配置 commitlint** 

```jsx
module.exports = {
  extends: ['@commitlint/config-conventional'],
};
```

配置文件的格式可以是下面的几种都可以：

- `.commitlintrc`
- `.commitlintrc.json`
- `.commitlintrc.yaml`
- `.commitlintrc.yml`
- `.commitlintrc.js`
- `.commitlintrc.cjs`
- `.commitlintrc.ts`
- `commitlint.config.js`
- `commitlint.config.cjs`
- `commitlint.config.ts`
- `commitlint` field in `package.json`

**3、测试**

- 命令行提交

![Untitled](/images/20220810/commit-15.png)

- vscode可视化提交

![Untitled](/images/20220810/commit-16.png)



**4、自定义校验规则**

以上使用的是commitlint的默认配置（遵循约定式提交规范），我们也可以自定义commit message的校验规则，比如增加type类型，正文之前是否需要空行等等。

rule配置说明:：rule由**名称**和**配置数组**组成，如：'name:[0, 'always', 72]'，

配置数组中有三个元素，分别为：

- `Level` ：为0，1，2三个数的其中一值。0代表让本规则无效；1代表以警告提示，不影响编译运行；2代表以错误提示，阻止编译运行。以上面的例子则代表该规则不会起作用。
- `Applicable`**：**第二位为应用与否，为always和never其中一值。
- `Value`：适用于该规则的值。

示例：

```jsx
/**
 * 
- `Level` ：为0，1，2三个数的其中一值。0代表让本规则无效；1代表以警告提示，不影响编译运行；2代表以错误提示，阻止编译运行。以上面的例子则代表该规则不会起作用。
- `Applicable`**：**第二位为应用与否，为always和never其中一值。
- `Value`：适用于该规则的值。
 */
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    // case可选值
      // 'lower-case' 小写 lowercase
      // 'upper-case' 大写 UPPERCASE
      // 'camel-case' 小驼峰 camelCase
      // 'kebab-case' 短横线 kebab-case
      // 'pascal-case' 大驼峰 PascalCase
      // 'sentence-case' 首字母大写 Sentence case
      // 'snake-case' 下划线 snake_case
      // 'start-case' 所有首字母大写 start-case
    'type-enum': [2, 'always', ['feat', 'fix', 'docs', 'style', 'ui', 'refactor', 'perf', 'test', 'build', 'ci', 'chore', 'revert']], // type的类型只能从中取值 
    'type-case': [2, 'always', 'lower-case'], // <type>格式小写
    'type-empty': [2, 'never'], // <type> 不能为空
    'type-min-length': [0, 'always', 0], 
    'type-max-length': [0, 'always', Infinity],

    'scope-enum': [0, 'always', []],
    'scope-case': [0, 'always', 'lower-case'], // <scope> 格式 小写
    'scope-empty': [2, 'never'], // <scope>为空
    'scope-min-length': [0, 'always', 0],
    'scope-max-length': [0, 'always', Infinity], 

    'subject-case': [0, 'always', 'lower-case'],
    'subject-empty': [2, 'never'], // <subject> 不能为空 (默认)
    'subject-full-stop': [0, 'always', '.'], // <subject> 以.为结束标志
    'subject-min-length': [0, 'always', 0],
    'subject-max-length': [0, 'always', Infinity], 
    'subject-exclamation-mark': [0, 'always'], // 在:前有感叹号

    'header-case': [0, 'always', 'lower-case'],
    'header-full-stop': [0, 'always', '.'],
    'header-min-length': [0, 'always', 0],
    'header-max-length': [0, 'always', Infinity],

    'body-case': [0, 'always', 'lower-case'],
    'body-leading-blank': [2, 'always'], // body前导空行
    'body-empty': [0, 'never'], // body为空
    'body-min-length': [0, 'always', 0], 
    'body-max-length': [0, 'always', Infinity],
    'body-max-line-length': [0, 'always', Infinity],

    'footer-leading-blank': [2, 'always'], // <footer> 前导空行
    'footer-empty': [0, 'never'],
    'footer-min-length': [0, 'always', 0],
    'footer-max-length': [0, 'always', Infinity],
    'footer-max-line-length': [0, 'always', Infinity],

    'references-empty': [0, 'never'], // 参考至少有一个
    'signed-off-by': [0, 'always', 'Signed-off-by:'], // message中含有value
    'trailer-exists': [0, 'always', 'Signed-off-by:'], // message尾部含有value
  },
};
```

> 目前规则必须遵守如下：
>
> - type必填（增加 ui 类型）
> - scope必填，影响范围写模块名（无法准确描述模块名的可以写笼统。比如国际云翻译）
> - body非必填，若填写则前面必须空一行
> - foot非必填，若填写则前面必须空一行



官方手册：[https://commitlint.js.org/#/reference-rules](https://commitlint.js.org/#/reference-rules)

参考示例：[https://github.com/conventional-changelog/commitlint/blob/master/@commitlint/config-conventional/index.js](https://github.com/conventional-changelog/commitlint/blob/master/@commitlint/config-conventional/index.js)

## 自动生成change log

使用 `conventional-changelog-cli` 可以自动生成Change log，生成的文档只包括以下3个部分：

- New features
- Bug fixes
- Breaking changes

**1、安装**

```
npm install conventional-changelog-cli -g
npm install conventional-changelog-cli -D
```

> 提示：使用**预设格式**需要全局安装，使用**自定义格式**需要项目安装。

**2、生成changelog**

```
conventional-changelog -p angular -i CHANGELOG.md -s
```

可以在package.json中加入配置方便使用

```jsx
"scripts": {
  "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s"
}
```

直接运行`npm run changelog`即可。

参数：

- `-p`：指定风格，以什么样式输出。以下是预设之一：
`angular`、`atom`、`codemirror`、`conventionalcommits`、`ember`、`eslint`、`express`、`jquery` 或 jshint
- `-s`：输出到相同文件（比如`CHANGELOG.md`）
- `-i`：指定输出的文件名称
- `-r`：从最新生成多少个版本。如果为 0，将重新生成整个变更日志。默认值：1
  
    ```
    conventional-changelog -p angular -i CHANGELOG.md -s -r 0 // 重新生成全部changelog
    ```
    

**3、自定义CHANGELOG格式**

由于默认的只能生成feat，fix，breaking的信息，如果想生成其他的log信息，就需要自定义CHANGELOG格式。需要从外部传入自定义配置文件。

- 安装依赖

```jsx
npm i conventional-changelog-custom-config compare-func -D
```

- `conventional-changelog-custom-config`：别人封装好的一个自定义CHANGELOG库

在根目录新建`changelog.config.js`文件（这个文件可以覆盖掉`conventional-changelog-custom-config` 中我们需要的配置）

```jsx
const compareFunc = require('compare-func')

const typeMap = {
  feat: '✨',
  fix: '🐛',
  docs: '📝',
  style: '💄',
  ui: '🎨',
  perf: '⚡',
  refactor: '🧩',
  test: '✅',
  revert: '⏪',
  build: '👷',
  ci: '🔩',
  chore: '🧱'
}

module.exports = {
  writerOpts: {
    transform: (commit, context) => {
      let discard = true
      const issues = []
      
      commit.notes.forEach(note => {
        note.title = 'BREAKING CHANGES'
        discard = false
      })
      
      if (commit.type && commit.type !== null) {
        commit.type = typeMap[commit.type] + ' ' + commit.type;
      } else
        return;

      if (commit.scope === '*') {
        commit.scope = ''
      }

      commit.subject = `${commit.type}: ` + commit.subject;

      // if (commit?.authorName) {
      //   commit.subject += `-- ${commit.authorName}.`
      // }

      if (typeof commit.hash === 'string') {
        commit.hash = commit.hash.substring(0, 7)
      }

      if (typeof commit.subject === 'string') {
        let url = context.repository ? `${context.host}/${context.owner}/${context.repository}` : context.repoUrl
        
        // console.log('==>', commit)
        
        if (url && commit.footer !== null) {
          // url = `${url}/issues/`
          // Issue URLs.
          const regexJIRA = /#([0-9A-Za-z]+-[0-9]+)/g;
          const regexZENTAO = /#([0-9]+)/g;
          // JIRA issues
          if (regexJIRA.test(commit.footer)) {
            let issues = commit.footer.match(regexJIRA);
            let issuesTxt = issues.map(issue => {
              return `[${issue}](http://10.8.40.130:8081/browse/${issue.replace('#', '')})`
            }).join('，')

            commit.subject = commit.subject + '（closes ' + issuesTxt + '）';
            
            // commit.footer = commit.footer.replace(/#([0-9A-Za-z]+-[0-9]+)/g, (match, issue) => {
            //   console.log('==>', match, issue)
              // issues.push(issue)
              // return `[#${issue}](${url}${issue})`
            // })
          }

          // 禅道 issues
          if (regexZENTAO.test(commit.footer)) {
            let issues = commit.footer.match(regexZENTAO);
            let issuesTxt = issues.map(issue => {
              return `[${issue}](http://10.8.8.162/zentao/bug-view-${issue.replace('#', '')}.html)`
            }).join('，')

            commit.subject = commit.subject + '（closes ' + issuesTxt + '）';
          }
        }

        if (context.host) {
          // User URLs.
          commit.subject = commit.subject.replace(/\B@([a-z0-9](?:-?[a-z0-9/]){0,38})/g, (_, username) => {
            if (username.includes('/')) {
              return `@${username}`
            }
            return `[@${username}](${context.host}/${username})`
          })
        }
      }

      // remove references that already appear in the subject
      // commit.references = commit.references.filter(reference => {
      //   if (issues.indexOf(reference.issue) === -1) {
      //     return false
      //   }
      //   return false
      // })
      commit.references = '';
      return commit
    },
    groupBy: 'committerDate',
    commitGroupsSort: 'type',
    commitsSort: ['scope', 'subject'],
    noteGroupsSort: 'title',
    notesSort: compareFunc
  }
}
```

修改 package.json 中 scripts 字段

```jsx
{
  "scripts": {
     "changelog": "conventional-changelog -p custom-config -i CHANGELOG.md -s -r 0  -n ./changelog.config.js"
  }
}
```

然后就可以生成下面的样子

![Untitled](/images/20220810/commit-17.png)

参考文档：

- [https://blog.csdn.net/qq_41887214/article/details/124353392](https://blog.csdn.net/qq_41887214/article/details/124353392)
- [https://juejin.cn/post/6844903888072654856](https://juejin.cn/post/6844903888072654856)



## 补充说明

以上只针对**单个仓库只有单个项目**的情况，如果一个仓库有多个独立项目，比如下面这种：

（以喻支付仓库为例）

![](/images/20220810/commit-20.png)

就需要在**仓库根目录下**，新增`package.json`文件，然后npm install安装commitizen，husky和commitlint等操作（就是把上面所有的操作放到仓库根目录），才能够校验commit。

这种情况下，每个子项目是**不需要任何安装和配置的**。



（完）



