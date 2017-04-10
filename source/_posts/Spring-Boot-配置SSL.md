---
title: Spring Boot 配置SSL
date: 2017-04-10 16:43:22
tags: 
- Java 
- Spring
- Spring Boot
- SSL
- HTTPS
---
# 生成ssl证书

## 证书颁发机构

### CA机构私钥

``` bash
$ openssl genrsa -out ca.key 2048
```
### CA证书
生成过程中需要输入一些CA机构信息

``` bash
$ openssl req -x509 -new -key ca.key -out ca.crt
```

## 服务端证书

### 生成服务端私钥

``` bash
$ openssl genrsa -out server.key 2048
```

### 生成服务端证书请求文件
生成过程中需要输入一些服务端信息

``` bash
$ openssl req -new -key server.key -out server.csr
```

### 使用CA证书生成服务端证书

``` bash
$ openssl x509 -req -sha256 -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -days 3650 -out server.crt
```
关于sha256，默认使用的是sha1，在新版本的chrome中会被认为是不安全的，因为使用了过时的加密算法

### 打包服务端的资料为pkcs12格式(非必要，只是换一种格式存储上一步生成的证书)

``` bash
$ openssl pkcs12 -export -in server.crt -inkey server.key -out server.pkcs12
```
生成过程中，需要创建访问密码


### 生成服务端的keystore（.jks文件, 非必要，Java程序通常使用该格式的证书）

``` bash
$ keytool -importkeystore -srckeystore server.pkcs12 -destkeystore server.jks -srcstoretype pkcs12
```
生成过程中，需要创建访问密码

### 把ca证书放到keystore中（非必要）

``` bash
$ keytool -importcert -keystore server.jks -file ca.crt
```


# 配置tomcat ssl信息
使用上面生成的jks证书

``` properties
server.ssl.key-store=classpath:证书.jks
server.ssl.key-store-password=证书密码
server.ssl.key-password=证书密码
```

# 总结
- crt、jks、pkcs12都是用来保存证书的不同格式，不同的服务器软件可能会使用不同格式的证书文件
- OpenSSl、Keytool都是可以用来生成Https证书的工具软件，其中OpenSSl功能更多更复杂，Keytool随JDK安装而安装