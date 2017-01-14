---
layout: post
title: idiomatic go
category: go
tags: [go]
---

# go编码的惯用方法

在编码和[重构](https://github.com/golang/go/wiki/CodeReviewComments)时，go有些惯用方法，下面介绍一些惯用方法。

## 某些单词的拼写要保持一致性
正确方式

```

// marshaling
// unmarshaling
// canceling
// cancelation

```
错误方式

```
// marshalling
// unmarshalling
// cancelling
// cancellation

```

对于Go项目本身的拼写一致性，这些单词有多重拼写方式，Go有选择其中一个。参考对比[this](https://github.com/golang/go/search?q=marshaling+OR+unmarshaling) vs [this](https://github.com/golang/go/search?q=marshalling+OR+unmarshalling)

## 句子间使用一个空格

正确方式

```
// Sentence one. Sentence two.

```

错误方式

```
// Sentence one.  Sentence two.

```

## 错误变量的命名

正确方式

```
// Package level exported error.
var ErrSomething = errors.New("something went wrong")

func main() {
	// Normally you call it just "err",
	result, err := doSomething()
	// and use err right away.

	// But if you want to give it a longer name, use "somethingError".
	var specificError error
	result, specificError = doSpecificThing()

	// ... use specificError later.
}
```

错误方式

```
var ErrorSomething = errors.New("something went wrong")
var SomethingErr = errors.New("something went wrong")
var SomethingError = errors.New("something went wrong")

func main() {
	var specificErr error
	result, specificErr = doSpecificThing()

	var errSpecific error
	result, errSpecific = doSpecificThing()

	var errorSpecific error
	result, errorSpecific = doSpecificThing()
}
```

## 品牌或单词开头大写，其他都小写

正确方式

```
// Exported.
var OAuthEnabled bool
var GitHubToken string

// Unexported.
var oauthEnabled bool
var githubToken string
```

错误方式

```
// Unexported.
var oAuthEnabled bool
var gitHubToken string
```

## 添加注释时在双划线后面添加一个空格

正确方式

```
// This is a comment
// for humans.

```

错误方式

```
//This is a comment
//for humans.
```

注意，注释是不添加空格的

```
//go:generate go run gen.go
```

## 在带来的影响是可忽略的，或者增加可读性时，可以使用defer

如果不能增强可读性，比如函数只有1-2步就执行完了，那么就不要使用它。

显然，使用defer会带来性能的影响，特别是在秒级操作方法上。

defer的[性能测试](https://lk4d4.darth.io/posts/defer/)

## 对于资源文件的名称使用单数形式

正确方式

```
golang.org/x/example/hello
golang.org/x/example/outyet
golang.org/x/mobile/example/basic
golang.org/x/mobile/example/flappy
github.com/shurcooL/tictactoe/player/bad
github.com/shurcooL/tictactoe/player/random

```
错误方式

```
golang.org/x/examples/hello
golang.org/x/examples/outyet
golang.org/x/mobile/examples/basic
golang.org/x/mobile/examples/flappy
github.com/shurcooL/tictactoe/players/bad
github.com/shurcooL/tictactoe/players/random

```

这是为了和go的风格保持一致

## 避免未使用的函数对象变量

正确方式

```
func (foo) method() {
	...
}

```

错误方式

```
func (f foo) method() {
	...
}
```

如果f未使用，那么可以清楚的知道，foo的方法和属性在method方法中未使用，增强了可读性。

## 空字符串校验

正确方式

```
if str == "" {
	...
}
```

错误方式

```
if len(str) == 0 {
	...
}
```

使用len来校验空字符串是正确的，但是len更适合在其他情况下使用。

第一种方式更具有可读性，可以清楚的字段str是字符串而不是数组

## 互斥锁保护罩

```
struct {
	...

	rateMu     sync.Mutex
	rateLimits [categories]Rate
	mostRecent rateLimitCategory
}
```

这里rateMu是一个互斥锁保护罩，它位于所保护的变量的顶部，好比一个帽子一样。所以无需对修改变量进行注释，和下面的效果是一样的。

```
struct {
	...

	// rateMu protects rateLimits and mostRecent.
	rateMu     sync.Mutex
	rateLimits [categories]Rate
	mostRecent rateLimitCategory
}
```

但新增无需rateMu保护的变量时

正确方式

```

 struct {
 	...

 	rateMu     sync.Mutex
 	rateLimits [categories]Rate
 	mostRecent rateLimitCategory
+
+	common service
 }
 
```
 
错误方式
 
```

 struct {
 	...

 	rateMu     sync.Mutex
 	rateLimits [categories]Rate
 	mostRecent rateLimitCategory
+	common     service
 }

```

## 不要在flag.Usage中使用os.Exit(2)
 
 正确方式
 
```
 flag.Usage = func() {
	fmt.Fprintf(os.Stderr, "Usage of %s:\n", os.Args[0])
	flag.PrintDefaults()
 }
```

错误方式

```
flag.Usage = func() {
	fmt.Fprintf(os.Stderr, "Usage of %s:\n", os.Args[0])
	flag.PrintDefaults()
	os.Exit(2)
}
```

flag包的Usage在处理错误时，已经有对退出进行处理，所以无需再次调用os.Exit(2)

# 参考资料

[https://dmitri.shuralyov.com/idiomatic-go](https://dmitri.shuralyov.com/idiomatic-go)
