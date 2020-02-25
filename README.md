# go-debug
    go项目打包为docker镜像

## 本地开发环境配置
    git check out 
    项目使用dep管理
    删除Gopkg.lock、Gopkg.toml文件
    cmd命令行（Golang IDE外开启终端）,执行:dep init -v,完成依赖加载，重新生成Gopkg.lock、Gopkg.toml文件
    可正常访问项目
 
 ## 项目结构
     chen@chen:~/go/src/go-debug$ tree -L 2
    .
    ├── cmd
    ├── Dockerfile
    ├── Gopkg.lock
    ├── Gopkg.toml
    ├── main.go
    ├── vendor
    │   ├── github.com
    │   └── golang.org
    └── _vendor-20200225104457
        ├── github.com
        └── golang.org

##  Dockerfile文件
    # build阶段
    FROM golang:1.10 AS build

    # Setting the working directory of our application
    WORKDIR /go/src/go-debug

    # Adding all the files required to compile the application
    ADD . .

    # Compiling a static go application (include C libraries in the built binary)
    RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo .

    # Set the startup command to be our application
    CMD ["./go-debug"]


    # 生产阶段(实现大幅度缩小镜像大小)
    FROM scratch AS prod
    # 从buil阶段拷贝二进制文件
    COPY --from=build /go/src/go-debug .
    CMD ["./go-debug"]

 
 ## 项目本地镜像打包、启动命令
       打包命令 ->  docker build -t 镜像名:版本号 .
       eg:  root@chen:/home/chen/go/src/go-debug# docker build -t echo-app:1.0.1 .
        
        启动命令 -> docker run --rm -ti -p 本地端口:镜像服务端口 镜像名:版本号
       eg : root@chen:/home/chen/go/src/go-debug# docker run --rm -ti -p 1324:1323 echo-app:1.0.1
    
    
## 上传镜像到阿里云镜像仓库
    $ sudo docker login --username=chenjiacheng@soundbus-rd registry.cn-hangzhou.aliyuncs.com
    $ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/sonicmoving/debug-service:[镜像版本号]
    $ sudo docker push registry.cn-hangzhou.aliyuncs.com/sonicmoving/debug-service:[镜像版本号]    
