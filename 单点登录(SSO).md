# 单点登录(SSO)

**Single Sign On**

- 在多个应用系统中，用户只需要登录一次就可以访问任何所有互相信任的应用系统
- 他包括将这次主要的登录映射到其他应用中用于同一个用户的登录机制。。

![image-20200611104145859](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200611104145859.png)

## SSL（Secure Socket Layer）

**SSL：** 安全套接字,位于可靠的面向连接的网络层协议和应用层协议之间的一种协议层。SSL通过互相认证，使用数字签名确保完整性，使用加密确保私密性，以实现客户端和服务器端之间的安全通讯。该协议由两层组成：SSL记录协议和SSL握手协议。

- SSL核心概念：加密算法，数字整数，CA

> OpenSSL简介

- 为网络通讯提供安全及数据完整性的一种安全协议，囊括了主要的密码算法，常用的密钥和证书封装管理功能以及SSL协议，并提供了丰富的应用程序供测试或其他目的使用。

-  通过在一定范围内部署一台CA(Certificate Authority)服务器可以实现局域网内的证书认证和授权，保证数据传输的安全性；也为企业级的证书管理提供知识积累。

## 1. CAS证书生成

1. 根证书

   ```xml
   openssl genrsa -out /srv/ftp/cas/cakey.pem 2048 RSA
   ```

生成一个使用RSA编码的密钥信息，而后生成到以上路径值中,该密钥对的长度为2048字节

生成根证书的签发申请

```xml
openssl req -new -key /srv/ftp/cas/cakey.pem -out /srv/ftp/cas
cacert.csr -subj /CN=cas.com
```

生成根证书的签发申请

```xml
openssl x509 -req -days 3650 -sha1 -extensions v3_ca -singnkey /srv/ftp/cas/cakey.pem
-in /srv/ftp/cas/cacert.csr -out /srv/ftp/cas/ca.cer
```

此签发证书的有效期为十年

## 2. 签发服务器端的证书

为了与根证书的保存区分,建议建立一个目录 ：mkdir -p /srv/ftp/cas/server

1. 生成服务器私钥

```xml
openssl genrsa -aes256 -out /srv/ftp/cas/server/server-key.pem 2048
```

服务器端生成私钥的时候需要设置密码。

2. 生成服务器端证书的签发和申请，需要输入第一步生成的密码

```xml
openssl req -new -key /srv/ftp/cas/server/server-key.pem -out /srv/ftp/cas/server/server.csr -subj /CN=cas.com
```



3. 生成服务器端证书的签发申请，有效期为10年

```xml
openssl x509 -req -days 3650 -sha1 -extensions v3_req -CA /srv/ftp/cas/ca.cer -CAkey /srv/ftp/cas/cakey.pem -CAserial /srv/ftp/cas/server/ca.srl -CAcreateserial  -in /srv/ftp/cas/server/server.csr -out /srv/ftp/cas/server/server.cer
```

此时有了服务器端证书之后才可以创建客户端证书

建立一个客户端文件夹保存客户端信息: mkdir -p /srv/ftp/cas/client;

-   生成客户端私钥

```xm
openssl genrsa -aes256 -out /srv/ftp/cas/client/client-key.pem 2048									
```

- 生成客户端的证书申请:

```xml
openssl req -new -key /srv/ftp/cas/client/client/client-key.pem -out /srv/ftp/cas/client/client.csr -subj /CN=cas.com	
```

- 生成客户端的签发证书:

![image-20200611112728318](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200611112728318.png)

此时最需要注意的是，该证书只针对于"cas.com" 有效 。

## 3. 生成Java证书

 现在使用了OpenSSL生成的数字证书和私钥，如果要像在java的环境下使用，需要将其转换为"PKCS#12" 的编码格式的密钥文件才可以被Java的keyTool工具所管理。

1. 生成客户端证书

```xml
openssl pkcs12 -export -clcerts -name cas-client -inkey /srv/ftp/cas/client/client-key.pem -in /srv/ftp/cas/client/client.cer -out /srv/ftp/cas/client/client.p12
```

客户端的证书随后是需要发给客户端浏览器的

2. 生成服务器端的证书

```xml
openssl pkcs12 -export -clcerts -name cas-server -inkey /srv/ftp/cas/server/server-key.pem -in /srv/ftp/cas/server/server.cer -out /srv/ftp/cas/server/server.p12
```

3. 将现在生成的服务器端证书导入到本机的受信任证书

```xml
keytool -importcert -trustcacerts -alias cas.com -file /srv/ftp/cas/ca.cer -keystore /usr/local/tomcat/ca-trust.p12
```

4.  查看所有的证书信息:

```xml
keytool -list -keystore /srv/ftp/cas/client/client.p12/ -storetypepkcs12 -v
```

随后还需要在客户端进行证书的配置！

==如果要想实现单点登录实际上并不需要用户自己去完成，单点登录只是一个设计思想，而很多的开发者在实际开发过程中，会专门配置一台CAS的认证服务器来实现单点登录==

## CAS（Central Authentication Service)

**具有以下特点:**

- 开源的企业级单点登录解决方案
- CAS Server为需要独立部署的web应用
- CAS Client支持非常多的客户端(指Web应用)，包括Java，.Net,PHP，Perl，Ruby等；

![image-20200611115713173](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200611115713173.png)

一旦你的项目中使用了CAS，那么CAS将作为服务器来访问

1. 普通的用户通过CAS客户端(Tomcat) 进行对Web的访问；
2. 而后WEB端会将用户的请求转发给CAS服务器端；
3. 跳转到CAS服务器端之后用户可以输入用户名和密码进行系统图登录
4. 如果认证通过该请求会返回到WEB端，随后Web端会接收一个CAS端生成的票根数据，并且使用此数据进行CAS认证
5. 票根检测通过后会将CAS服务器端的用户名发送回WEB端





























