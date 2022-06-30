# Lab0

- MIT6.824的环境搭建与基本运行
  
  - 实验官网链接：[6.824 Lab 1: MapReduce](https://pdos.csail.mit.edu/6.824/labs/lab-mr.html)

- 前提知识：
  
  - 电脑已经基本配置了Go的运行环境
  - Golang的基本语法和基本运行

## 1. Golang再理解

- go mod?
  
  - Go.mod是Golang1.11版本新引入的官方包管理工具用于解决之前没有地方记录依赖包具体版本的问题，方便依赖包的管理。
  
  - 之前只能通过GOPATH的方法导入其他包下的.go文件，使用go mod可以更加方便的实现包的管理
  
  - 如何使用？
    
    - go env查看当前GO111MODULE是否打开？
    
    - GO111MODULE=off，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
    
    - GO111MODULE=on，go命令行会使用modules，不会去GOPATH目录下查找。
    
    - GO111MODULE=auto，默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：
      
      - 当前目录在GOPATH/src之外且该目录包含go.mod文件
      
      - 当前文件在包含go.mod文件的目录下面。
  
  - ```go
    // 前置知识
    // GOROOT：Golang的安装路径，安装之后就默认自带了
    // GOPATH：Golang的工作目录
    // 导包运行时出错
    // 查看go的环境配置 go env
    // 方法一：使用go mod模式
    king@kingdeMacBook-Pro main % go env -w GO111MODULE=on   
    king@kingdeMacBook-Pro main % go mod init project01
    king@kingdeMacBook-Pro main % go mod tidy
    // 方法二：不使用go mode模式
    king@kingdeMacBook-Pro main % go env -w GO111MODULE=off
    king@kingdeMacBook-Pro main % go env -w GOPATH=/Users/king/Documents/Go_Study/Go_InitStudy     
    ```

- go build?
  
  - 编译指定文件，如果不存在main则编译出来的不可运行
  
  - 所以如果是普通包，执行go build不会产生任何文件，似乎有一个.so文件
  
  - 如果是main包，则能够编译出可执行文件

- go xxx -race xxx.go?
  
  - race开启竞争检测！
  
  - 竞争检测：顾名思义，就是检查多协程情况下是否会出现资源竞争、死锁等情况

## 2. 获取实验代码-简单运行

- 按照官方Lab获取代码
  
  - ```bash
    $ git clone git://g.csail.mit.edu/6.824-golabs-2022 6.824
    $ cd 6.824
    $ ls
    Makefile src
    $ cd ~/6.824
    $ cd src/main
    $ go build -race -buildmode=plugin ../mrapps/wc.go
    ```

- 简单运行-遇到的问题：
  
  - 会报路径错误！
  
  - 如何解决？
    
    - 官方的6.824带了go.mod
    
    - 所以开启GO111MODULE即可简单运行！
    
    - ```bash
      $ go env -w GO111MODULE=on   
      ```

- 继续按照官方教程运行测试即可

- ```bash
  # go build -buildmode=plugin ../mrapps/wc.go该命令的作用是构建 mrapps 的动态链接库，
  # 使用了 Golang 的  Plugin 来构建 mrapps，
  # 使得 mr 框架的代码可以和 mrapps 的代码分开编译，
  # 而后 mr 框架再通过动态链接的方式载入指定的 mrapps 运行。
  $ go build -race -buildmode=plugin ../mrapps/wc.go
  $ rm mr-out*
  $ go run -race mrsequential.go wc.so pg*.txt
  $ more mr-out-0
  ```

- 自此基本的环境搭建与运行就完成了

- 后续认真阅读lab要求进行code即可
