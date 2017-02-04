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

## 方法2：strings.Join

## 方法3：bytes.Buffer

## 参考资料

[http://herman.asia/efficient-string-concatenation-in-go](http://herman.asia/efficient-string-concatenation-in-go)
