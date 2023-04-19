# go 串口
貌似原始仓库作者很多年不更新，本仓库把原始仓库底下的一些不错的pr合并进来了，所以原始文档直接查看 `https://github.com/tarm/serial` 即可。
## 快速开始
```go
package main

import (
        "log"

        "github.com/wwhai/tarmserial"
)

func main() {
        c := &serial.Config{Name: "COM45", Baud: 115200}
        s, err := serial.OpenPort(c)
        if err != nil {
                log.Fatal(err)
        }

        n, err := s.Write([]byte("test"))
        if err != nil {
                log.Fatal(err)
        }

        buf := make([]byte, 128)
        n, err = s.Read(buf)
        if err != nil {
                log.Fatal(err)
        }
        log.Printf("%q", buf[:n])
}
```
## 超时
新增了个读写超时控制:
```go
s1.SetReadDeadline(time.Now().Add(30 * time.Millisecond))
n, err := s1.Read(buf)
s1.SetReadDeadline(time.Time{})
```
原来的仓库使用的是全局 `serial.Config{}`, 里面有个 `ReadTimeout` 参数，因为这个参数是全局参数，一次配置无法修改，所以不适合动态读写超时控制，所以在原来的基础上扩充了标准的`Context Timeout`形式。`SetReadDeadline(time.Time{})` 表示取消本次超时控制, 这样看起来就和TCP等网络操作形式一样了。