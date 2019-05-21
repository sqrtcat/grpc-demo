# grpc-demo
服务端使用 Go，客户端使用 Go / PHP

## install protobuf
安装 protobuf，使用 protoc 编译服务的 proto3 IDL 接口
```
./configure
make 
make install
protoc --help
```
## install protoc-gen-go
```
go get -u github.com/golang/protobuf/protoc-gen-go
```
借助 protoc-gen-go 插件可以生成更友好的 Go Server/Client 代码库，方便直接调用服务接口

## install grpc-go
Go 的 grpc 依赖库
```
# go get google.golang.org/grpc 墙内的孩子们不太方便用 手动装库吧
git clone https://github.com/grpc/grpc-go.git $GOPATH/src/google.golang.org/grpc
git clone https://github.com/golang/net.git $GOPATH/src/golang.org/x/net
git clone https://github.com/golang/text.git $GOPATH/src/golang.org/x/text
git clone https://github.com/google/go-genproto.git $GOPATH/src/google.golang.org/genproto
cd $GOPATH/src/
go install google.golang.org/grpc
```
## User 服务接口 proto3 IDL
```
syntax = "proto3";
 
// user 包
package user;

// User 服务及服务接口的定义
service User {
    rpc UserIndex(UserIndexRequest) returns (UserIndexResponse) {}
    rpc UserView(UserViewRequest) returns (UserViewResponse) {}
    rpc UserPost(UserPostRequest) returns (UserPostResponse) {}
    rpc UserDelete(UserDeleteRequest) returns (UserDeleteResponse) {}
}

// 用户实体模型
message UserEntity {
    string name = 1;
    int32 age = 2;
}

// User 服务的各个接口的请求/响应结构
message UserIndexRequest {
    int32 page = 1;
    int32 page_size = 2;
}

message UserIndexResponse {
    int32 err = 1;
    string msg = 2;
    // 返回一个 UserEntity 对象的列表数据
    repeated UserEntity data = 3;
}

message UserViewRequest {
    int32 uid = 1;
}

message UserViewResponse {
    int32 err = 1;
    string msg = 2;
    // 返回一个 UserEntity 对象
    UserEntity data = 3;
}

message UserPostRequest {
    string name = 1;
    string password = 2;
    int32 age = 3;
}

message UserPostResponse {
    int32 err = 1;
    string msg = 2;
}

message UserDeleteRequest {
    int32 uid = 1;
}

message UserDeleteResponse {
    int32 err = 1;
    string msg = 2;
}
```
## 生成 Go 服务端/客户端
生成 Go 服务端/客户端
```
protoc -I. --go_out=plugins=grpc:./user user.proto
```
## 生成 PHP 客户端
### 安装 grpc_php_plugin 插件
借助 grpc_php_plugin 可以方便我们使用 protoc 生成封装的更为友好的客户端代码（client with stub）
```
#grpc 内置了多种语言的插件 但当前还没有 go 的，所以 go 单独用 get 安装了 protoc-gen-go
git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc
cd grpc && git submodule update --init
# 这里我们只编译 php 的插件 如果要编译所有的 make && make install
make grpc_php_plugin
# 记住插件路径
ll ./bins/opt/grpc_php_plugin
```
### 生成 php client with stub
注意 protoc-gen-grpc 路径的正确性
```
protoc -I. \
--php_out=./user \
--grpc_out=./user \
--plugin=protoc-gen-grpc=/root/grpc/bins/opt/grpc_php_plugin \
user.proto
```
## server run
go run server.go

## client run
```
go run client.go
# php
cd php-client
# 安装扩展
pecl install grpc
pecl install protobuf
# 安装依赖
composer install
php client.php
