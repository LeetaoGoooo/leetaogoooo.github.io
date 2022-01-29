---
title: 'git commit 规范以及校验方案'
date: 2021-11-19 07:55:16
tags: [gitflow]
published: true
hideInList: false
feature: 
isTop: false
---
# 前言

在使用 Git 作为版本控制工具时，每次文件发生修改的时候提交都需要 `git commit` 命令去记录本次的修改，否则就不允许提交，显然 `git commit`  是一个重要的环节，因此制定一个 Git Commit 规范是有必要的，否则就会出现混乱的提交信息，这里腾讯某团队的规范作为例子，然后通过一定的手段去帮助我们把这个规范落到实处。

目的：

- 统一团队 `Git commit` 日志标准，便于后续代码 review，版本发布以及日志自动化生成等等。
- 统一团队的 Git 工作流，包括分支使用、tag 规范、issue 等

# **Git commit 日志参考案例**

- [angular](https://github.com/angular/angular)
- [commit-message-test-project](https://github.com/cpselvis/commit-message-test-project)
- [babel-plugin-istanbul](https://github.com/istanbuljs/babel-plugin-istanbul)
- [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog)

# **总体方案**

![来自 feflow 的 git commit 规范](https://www.leetao94.cn/post-images/1637279828697.png)


# **Git commit日志基本规范**

```python
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

## **type 类型**

> type代表某次提交的类型，比如是修复一个bug还是增加一个新的feature
> 
- feat： 新增 feature
- fix: 修复 bug
- docs: 仅仅修改了文档，比如 README, CHANGELOG, CONTRIBUTE等等
- style: 仅仅修改了空格、格式缩进、逗号等等，不改变代码逻辑
- refactor: 代码重构，没有加新功能或者修复 bug
- perf: 优化相关，比如提升性能、体验
- test: 测试用例，包括单元测试、集成测试等
- chore: 改变构建流程、或者增加依赖库、工具等
- revert: 回滚到上一个版本

## **格式要求**

```python
# 标题行：50个字符以内，描述主要变更内容
#
# 主体内容：更详细的说明文本，建议72个字符以内。 需要描述的信息包括:
#
# * 为什么这个变更是必须的? 它可能是用来修复一个bug，增加一个feature，提升性能、可靠性、稳定性等等
# * 他如何解决这个问题? 具体描述解决问题的步骤
# * 是否存在副作用、风险?
#
# 尾部：如果需要的化可以添加一个链接到issue地址或者其它文档，或者关闭某个issue。
```

# 校验方案

node 项目已经有比较成熟的方案，这里以 Python 项目为例，考虑实际使用的便利性，希望可以可以实现以下的目的：

1. 成员在**本地执行 git commit 的命令时就完成校验**，通过则允许执行 `git push` 否则则需要重新提交 commit 的信息。
2. 提交到 gitlab，由服务端完成再次校验

## 本地校验

### git hooks

Git 在执行 `git init` 进行初始化的时候，会在 `.git/hooks` 目录下生成一系列 hooks 脚本:

![git-hooks](https://www.leetao94.cn/post-images/1637279929295.png)

从上图可以看到每个脚本的后缀都是以 `.sample` 结尾的，在这个时候，脚本是不会自动执行的。我们需要把后缀去掉之后才会生效，即将 `pre-commit.sample` 变成 `pre-commit` 才会起作用。由于只是对 commit msg 做校验，所以只需要使用 `commit-msg` 脚本即可。

### commit-msg 脚本

在 `commit-msg` hooks 中完成对 commit 消息校验

```python
#!/bin/sh

# 获取当前提交的 commit msg
commit_msg=`cat $1`

msg_re="^(feat|fix|docs|style|refactor|perf|test|workflow|build|ci|chore|release|workflow)(\(.+\))?: .{1,100}"

if [[ ! $commit_msg =~ $msg_re ]]
then
    echo "\n不合法的 commit 消息提交格式，请使用正确的格式\n <type>(<scope>): <subject>"
    # 异常退出
    exit 1
fi
```

将上述脚本保持在项目所在对 `.git/hooks` 目录下命名为 `commit-msg`,然后执行 `chmod` 命令：

```python
chmod +x .git/hooks/commit-msg
```

### 验证结果

1. 不符合规法的 commit msg

![不合规的git-commit-msg](https://www.leetao94.cn/post-images/1637280036579.png)

2. 符合规范的 commit-msg

![合规的git-commit-msg](https://www.leetao94.cn/post-images/1637280050146.png)

# 服务端校验

Git 在服务端也同样有一些 hooks:

- `pre-receive`
- `update`
- `post-receive`

每个 hooks 的具体功能可以参考 [Server-Side Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks#_server_side_hooks) ，其中 `pre-receive` 和 `update` 均符合使用场景，唯一区别是**用户同时推送到多个分支时， update 针对每个分支都会被触发执行，而 `pre-receive` 只执行一次。**

## pre-receive

### 说明

在任何文件被更新时，如果`$GIT_DIR/hooks/pre-receive` 存在并且是可执行的文件，则 pre-receive 会被无参数触发执行一次，正常 pre-receive 触发执行的时候会接收如下的参数：

```bash
sha1-old SP sha1-new SP refname LF
```

其中 sha1-old 为多次 commit 的最早一次的 commit 的 id，而 sha1-new 则是最新一次的 id。除此之外，git push 的时候还会传递其他的信息，可以参考 [pre-receive-hooks](https://git-scm.com/docs/git-receive-pack#_pre_receive_hook) 。

### 校验方法

通过 `git log old-commit-ID new-commit-ID -pretty=format:%s` 提取出俩个 commit 之间的所有 commit-msg 然后逐一校验。

![](https://www.leetao94.cn/post-images/1637280064019.png)

考虑易用性，用 golang 构建校验脚本

```golang
package main

import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"os"
	"os/exec"
	"regexp"
	"strings"
)

type CommitTypeConfig struct {
	commitTypeList []string
	strictMode     bool // 严格模式下将校验所有的提交信息格式(多 commit 下)
}

const checkFailedMeassge = `
Commit message 格式校验失败❌
Commit message 格式必须符合下述规则:
    ^(\w+)(\(\w+\))*: (.+)|^Merge\ branch(.*)|^Merge\ remote-tracking branch(.*)
Example:
    feat(test): test commit style check.`

const ZERO_COMMIT = "0000000000000000000000000000000000000000"

func main() {
	config := CommitTypeConfig{
		commitTypeList: []string{"feat", "fix", "docs", "style", "refactor", "test", "chore", "perf", "hotfix"},
		strictMode:     true}
	input, _ := ioutil.ReadAll(os.Stdin)
	param := strings.Fields(string(input))
	// allow branch/tag delete
	if param[1] == ZERO_COMMIT {
		os.Exit(0)
	}
	if isExists("pre-receive.json") {
		fmt.Println("检测到存在配置文件，加载配置文件规则💪")
		file, _ := ioutil.ReadFile("pre-receive.json")
		tmpConfig := CommitTypeConfig{}
		err := json.Unmarshal([]byte(file), &tmpConfig)
		if err == nil {
			fmt.Println("成功加载配置文件规则😊")
			config = tmpConfig
		} else {
			fmt.Println("加载配置文件规则失败，使用默认规则文件😭")
		}
	} else {
		fmt.Println("开始加载默认配置文件规则💪")
	}
	commitMsg := getCommitMsg(param[0], param[1])
	checkCommitMsg(commitMsg, config)
}

func getCommitMsg(oldCommitID string, commitID string) []string {
	s := fmt.Sprintf("git log %s..%s --pretty=format:%%s", oldCommitID, commitID)
	if oldCommitID == ZERO_COMMIT {
		s = fmt.Sprintf("git rev-list --pretty=format:%%s $(git for-each-ref --format='%%(refname)' refs/heads/* | sed 's/^/\\^/g') %s | grep -v ^commit", commitID)
	}
	commitMsg := strings.Split(runCmd(s), "\n")
	return commitMsg
}

func checkFailed(tmpStr string) {
	fmt.Fprintln(os.Stderr, "===================================")
	fmt.Fprintln(os.Stderr, tmpStr)
	fmt.Fprintln(os.Stderr, "===================================")
	fmt.Fprintln(os.Stderr, checkFailedMeassge)
	os.Exit(1)
}

func isExists(path string) bool {
	_, err := os.Stat(path)
	if err == nil {
		return false
	}
	if os.IsNotExist(err) {
		return false
	}
	return true
}

/// 规则校验
/// @commitMsg []string 提交的信息数组
/// @config 规则配置
func checkCommitMsg(commitMsg []string, config CommitTypeConfig) {
	var CommitMessagePattern = `^(\w+)(\(\w+\))*: (.+)|^Merge\ branch(.*)|^Merge\ remote-tracking branch(.*)`
	var commitMsgReg = regexp.MustCompile(CommitMessagePattern)
	for _, tmpStr := range commitMsg {
		if strings.Trim(tmpStr, " ") == "" {
			continue
		}
		commitTypes := commitMsgReg.FindAllStringSubmatch(tmpStr, -1)
		if len(commitTypes) != 1 {
			checkFailed(tmpStr)
		} else {
			if !strings.HasPrefix(tmpStr, "Merge") && !stringInSlice(commitTypes[0][1], config.commitTypeList) {
				checkFailed(tmpStr)
			}
		}
		if !config.strictMode {
			fmt.Println("恭喜🎉，commit message 校验通过✅")
			os.Exit(0)
		}
	}
	fmt.Println("恭喜🎉，commit message 校验通过✅")
}
func stringInSlice(a string, list []string) bool {
	for _, b := range list {
		if b == a {
			return true
		}
	}
	return false
}

func runCmd(s string) string {
	cmd := exec.Command("/bin/bash", "-c", s)
	cmd.Stdin = os.Stdin
	cmd.Stderr = os.Stderr
	r, err := cmd.Output()
	if err != nil {
		fmt.Printf("Run command failed, [%s]\nerr: %s", s, err)
	}
	return string(r)
}
```

同时支持添加配置文件

```json
{
    "commitTypeList":[ // 支持的 commitType
        "feat",
        "fix",
        "docs",
        "style",
        "refactor",
        "test",
        "chore",
        "perf",
        "hotfix"
    ],
    "strictMode": true // 严格模式会校验多次 commit 的所有信息
}
```

## 服务器配置 pre-receive

参考 [Server Hooks](https://docs.gitlab.com/ee/administration/server_hooks.html) 需要将编译后 `pre-receive` 放到制定的 repository 的钩子目录即可，具体步骤如下:

1. 找到对应 repository 的 `.git` 目录
2. 在该目录下创建 `custom_hooks` 的目录
3. 将编译后的 `pre-receive` 放到该目录下(如果有配置文件也上传到该目录下)
4. 通过 `chmod +x pre-receive` 让该文件可执行，同时将该文件的用户组切换为 `git:git`
5. 推送代码验证结果

![pre-receive-校验通过结果](https://www.leetao94.cn/post-images/1637280076441.png)

- 参考链接
    - [pre-receive-hooks](https://git-scm.com/docs/git-receive-pack#_pre_receive_hook)
    - [Server Hooks](https://docs.gitlab.com/ee/administration/server_hooks.html)
    - [Server-Side Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks#_server_side_hooks)
    - [Git Commit 规范](https://feflowjs.com/zh/guide/rule-git-commit.html)
    - [git commit 规范指南](https://segmentfault.com/a/1190000009048911)
    - [git-commit](https://git-scm.com/docs/git-commit)
    - [如何规范你的Git commit？](https://zhuanlan.zhihu.com/p/182553920)