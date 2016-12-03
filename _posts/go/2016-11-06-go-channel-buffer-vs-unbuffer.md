---
layout: post
title: channel buffer vs unbuffer
category: go
tags: [go]
---

下面介绍下channel有缓冲区和无缓冲区的区别：

无缓冲区的channel会同时阻塞发送方和接收方，直接双方都准备好。有缓冲区的channel只会在缓冲区满后阻塞发送方。

下面是一个小例子

...

var c = make(chan int, 5)

func main() {
  go worker(1)
  for i := 0; i < 10; i++ {
    c <- i
    println(i)
  }
}

func worker(id int) {
  for {
    _ = <-c
    time.Sleep(time.Second)
  }
}

...

上面例子中有一个缓冲区大小为5的channel和一个每次接收数据都睡眠1秒的worker。很明显channel的缓冲区会被快速的填满数据然后阻塞，直到worker开始消费数据。
你会看到0,1,2,3,4,5立即输出，然后6,7,8,9每秒输出一个。有缓冲区的channel是基于0开始的，所以你会看到0-5，而不是0-4。

缓冲区的channel有何用处呢？让我们来看个简单但是真实的例子：记录web服务请求的日志。我们要记录的服务请求，在并发量大时请求数会达到几千个每秒，为了提高
性能，我们会先把日志缓存在内存中，并定时写到硬盘上。先来看下无缓冲区的channel实现：

...

var channel = make(chan []byte)
func init() {
  go worker(8192)
}

func Log(req *http.Request) {
  channel <- createLog(req)
}

func createLog(req *http.Request) []byte {
  var buffer bytes.Buffer
  buffer.Write([]byte(req.RemoteAddr))
  buffer.Write([]byte("\t"))
  buffer.Write([]byte(req.URL.Path))
  buffer.Write([]byte("\n"))
  return buffer.Bytes()
}

func worker(size int) {
  buffer := make([]byte, size)
  position := 0
  for {
    entry := <- channel
    length := len(entry)
    if length > size {
      //todo handle a message that we can't fit in our buffer (log an error? save directly to disk?)
    }
    if (length + position) > size {
      //todo flush buffer to disk
      //we'll want to write buffer[0:position] to avoid including data from a previous pass
      position = 0
    }
    copy(buffer[position:], entry)
    position += length
  }
}

...

首先，声明了一个channel和worker，Log方法会根据request构造日志信息并发送给channel，worker会从channel中接收到日志信息，并判断缓存是否还有空间，若有
则放到缓存，否则先把缓存中的数据写入硬盘中。

在正常情况下，能够运行的很好，因为都是在内存中复制bytes。但是当缓存数据需要写入硬盘时，处理request的goroutine将会阻塞当往channel写日志信息时
(channel <- createLog(req))。如何解决？可以通过缓冲区和更多的worker来解决，只需部分改动下：

...

const workerCount = 4
var channel = make(chan []byte, workerCount)
func init() {
  for i := 0; i < workerCount; i++ {
    go worker(8192)
  }
}

...

当一个worker在忙于写硬盘时，其他空闲worker可以继续处理日志信息。
