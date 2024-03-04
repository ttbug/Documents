# Golang二三事

## 交叉编译
```shell
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build hello.go
```
一些常用的GOOS和GOARCH支持的参数

| GOOS | GOARCH |
| --- |---|
| linux | arm64 |
| linux | amd64 |
| windows | amd64 |
| windows | arm64 |
| darwin | arm64 |
| darwin | amd64 |




