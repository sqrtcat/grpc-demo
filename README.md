# grpc-demo
grpc demo with Golang S/C and PHP client

# protoc
### go server and client
protoc -I. --go_out=plugins=grpc:./user user.proto

### php client with stub
protoc -I. \
--php_out=./user \
--grpc_out=./user \
--plugin=protoc-gen-grpc=/root/grpc/bins/opt/grpc_php_plugin \
user.proto

# server
go run server.go

# client
go run go-client.go
cd php-client && php index.php
