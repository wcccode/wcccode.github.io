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


# 参考资料

[https://dmitri.shuralyov.com/idiomatic-go](https://dmitri.shuralyov.com/idiomatic-go)
