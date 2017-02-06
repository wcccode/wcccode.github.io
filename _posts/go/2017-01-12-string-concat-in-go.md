---
layout: post
title: Efficient String Concatenation in Go
category: go
tags: [go]
---

# Go中的高效字符串拼接

本文将比较Go语言中各种不同的字符串拼接方法的性能。为了比较，准备了一些基准测试指标，程序如下

```
func BenchmarkBufferString(b *testing.B, numConcat int) {
    var ns string
    for i := 0; i < b.N; i++ {
        next := nextString()
        buffer := bytes.NewBufferString("")
        for u := 0; u < numConcat; u++ {
            buffer.WriteString(next())
        }
        ns = buffer.String()
    }
    global = ns
}
```

为了测试这些指标，我想需要一个方法返回一些字符串，然后把这些字符串拼接成一个新的字符串。这边写了个简单的方法，每次调用返回当前的索引值：

```
// nextString is an iterator we use to represent a process
// that returns strings that we want to concatenate in order.
func nextString() func() string {
    n := 0
    // closure captures variable n
    return func() string {
        n += 1
        return strconv.Itoa(n)
    }
}
```

我们测试的方法都会在循环中调用上面的方法，最后的输入如：0123456789101112131415...。考虑到函数调用的开销，因为都有函数调用开销，所以可以忽略不计。现在已经设置好了测试基准，让我们来看看这些方法的的测试性能

## 方法1：+=

最直接的拼接字符串的方法是使用+=。由于Go中的字符串是不可变的，所以每次字符串的拼接都会生成新的字符串，分配新的内存来存储，这与C++和Basic是不同的。在Go中，每次拼接2个字符串，都会创建一个新的字符串空间，并拷贝2个字符串。当拼接的字符串长度过长时，拷贝会变的很慢，这种方法的时间复杂度是O(N^2)。

## 方法2：strings.Join
Go的标准库strings提供了join的方法，该方法可以把一个字符串数组拼接在一起。在本次测试例子中，因为没有字符串数组，所以首先需要构建一个字符串数组，构建字符串数组会消耗一些时间，所以会把这部分时间算在测试基准当中。最终拼接会消耗O(N)，但构建字符串消耗了O(N)（平均值），空间消耗O(N)（字符串数组所消耗的空间）。

这边有个使用strings.Join的例子

```
s := []string{}
for i := 0; i < 1000; i++ {
    s = append(s, "a")
}
fmt.Println(strings.Join(s, ""))
```

## 方法3：bytes.Buffer

最后一种方式是使用Go中bytes包的Buffer类型，bytes.Buffer实现了io.Writer，可以在O(N)的时间复杂度下完成字符串的拼接，同时无需额外的内存空间来储存字符串数组。

下面是使用bytes.Buffer的例子

```
var buffer bytes.Buffer
for i := 0; i < 1000; i++ {
    buffer.WriteString("a")
}
fmt.Println(buffer.String())

```

现在已经了解完了3种字符串拼接的方法，下面开始执行测试，我会先跳过各种测试中的具体过程，直接来看下那个方法最优。

## 最佳的是 方法3：bytes.Buffer

方法3是最佳的方式，但并不是在所有情况下。下面是比较3种方式，在10000个字符串（小于5个字符），共37KB下时候的测试情况

-- 图片 --

上图展示了bytes.Buffer以很大的优势获胜

下面看下每次25个字符，共244KB的情况

-- 图片 --

看上去strings.Join和bytes.Buffer差不多，但是还是没bytes.Buffer优秀

我也测试了一些小数量的字符串拼接，这种情况下方法1：+=还是稍微比其他方法快些，所以方法1还是可以用于小量的字符串拼接。

最后，让我们看下方法2中，字符串数组构建的影响有多大。排除字符串数组构建的耗时，同时在遍历时调用nextString，所以测试还是公平的，在来看下测试结果

-- 图片 --

当我们忽略方法2的字符串数组构建消耗，那么方法2比方法3略胜一筹。

## 总结
综上，对于小量的短字符串（少于100个，长度小于10）拼接使用方法1。对于大量字符串，同时需要高效率时，使用方法3，bytes.Buffer是3种方法中最好的。对于需要对已有的字符串数组进行拼接时，使用方法2。

## 测试过程

下面是对于不同方法，不同拼接数量在我机器（Go 1.2 on Ubuntu 64bit, Intel® Core™ i7-3667U CPU @ 2.00GHz）上执行go test -bench的结果

```
BenchmarkNaiveConcat10    500000          6403 ns/op
BenchmarkNaiveConcat100    20000         77001 ns/op
BenchmarkNaiveConcat1000         500       3105361 ns/op
BenchmarkNaiveConcat10000          5     236872134 ns/op
BenchmarkJoin10   200000          8129 ns/op
BenchmarkJoin100       50000         51891 ns/op
BenchmarkJoin1000       5000        465179 ns/op
BenchmarkJoin10000       500       5911317 ns/op
BenchmarkBufferString10   200000          7688 ns/op
BenchmarkBufferString100       50000         45027 ns/op
BenchmarkBufferString1000       5000        398311 ns/op
BenchmarkBufferString10000       500       3702906 ns/op
```

你也可以自己执行下，代码下载[https://github.com/hermanschaaf/go-string-concat-benchmarks](https://github.com/hermanschaaf/go-string-concat-benchmarks)。 一般的测试基准如下

```
var global string

// benchmarkNaiveConcat provides a benchmark for basic built-in
// Go string concatenation. Because strings are immutable in Go,
// it performs the worst of the tested methods. The time taken to
// set up the array that is appended is not counted towards the
// time for naive concatenation.
func benchmarkNaiveConcat(b *testing.B, numConcat int) {
    var ns string
    for i := 0; i < b.N; i++ {
        next := nextString()
        ns = ""
        for u := 0; u < numConcat; u++ {
            ns += next()
        }
    }
    // we assign to a global variable to make sure compiler
    // or runtime optimizations don't skip over the operations
    // we were benchmarking. This might be unnecessary, but it's
    // safer.
    global = ns
}
```

## 参考资料

[http://herman.asia/efficient-string-concatenation-in-go](http://herman.asia/efficient-string-concatenation-in-go)
