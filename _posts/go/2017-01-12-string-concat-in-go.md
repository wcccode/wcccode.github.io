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

## 参考资料

[http://herman.asia/efficient-string-concatenation-in-go](http://herman.asia/efficient-string-concatenation-in-go)
