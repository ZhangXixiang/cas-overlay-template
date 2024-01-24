
```Mac环境注意点：
参考链接：https://blog.csdn.net/qq_34021712/article/details/80871015

1、确定java版本，删除不需要的版本；
查找当前已安装的Java版本
//输入：
ls /Library/Java/JavaVirtualMachines/
//输出：
jdk1.8.0_211.jdk jdk-11.0.1.jdk
选择想要卸载的版本，进行卸载
sudo rm -rf /Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk

2、确定maven打包运行使用的java版本是1.8，mvn-version

3、证书相关
####生成keystore
keytool -genkey -alias tomcat -keyalg RSA -validity 3650 -keystore /Users/mac/Desktop/tomcat.keystore

#输入第一步中keystore的密码changeit
keytool -export -alias tomcat -file /Users/mac/Desktop/tomcat.cer -keystore /Users/mac/Desktop/tomcat.keystore -validity 3650

####查看指定目录的证书
keytool -list -keystore /Users/mac/Desktop/tomcat.keystore 
####删除已有的证书
sudo keytool -delete -alias tomcat -keystore /Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home/jre/lib/security/cacerts

#授权文件到jdk
sudo keytool -import -keystore /Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home/jre/lib/security/cacerts -file /Users/mac/Desktop/tomcat.cer -alias tomcat -storepass changeit

#配置到tomcat的8443端口，进行证书绑定
<Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="E:\projects\learn\cas\tomcat.keystore" keystorePass="123456"
           clientAuth="false" sslProtocol="TLS"/>

4、默认账号：casuser 默认密码：Mellon 仅有这一个用户

```




CAS Overlay Template
============================

Generic CAS WAR overlay to exercise the latest versions of CAS. This overlay could be freely used as a starting template for local CAS war overlays. The CAS services management overlay is available [here](https://github.com/apereo/cas-services-management-overlay).

# Versions

```xml
<cas.version>5.3.x</cas.version>
```

# Requirements

* JDK 1.8+

# Configuration

The `etc` directory contains the configuration files and directories that need to be copied to `/etc/cas/config`.

# Build

To see what commands are available to the build script, run:

```bash
./build.sh help
```

To package the final web application, run:

```bash
./build.sh package
```

To update `SNAPSHOT` versions run:

```bash
./build.sh package -U
```

# Deployment

- Create a keystore file `thekeystore` under `/etc/cas`. Use the password `changeit` for both the keystore and the key/certificate entries.
- Ensure the keystore is loaded up with keys and certificates of the server.

On a successful deployment via the following methods, CAS will be available at:

* `http://cas.server.name:8080/cas`
* `https://cas.server.name:8443/cas`

## Executable WAR

Run the CAS web application as an executable WAR.

```bash
./build.sh run
```

## Spring Boot

Run the CAS web application as an executable WAR via Spring Boot. This is most useful during development and testing.

```bash
./build.sh bootrun
```

### Warning!

Be careful with this method of deployment. `bootRun` is not designed to work with already executable WAR artifacts such that CAS server web application. YMMV. Today, uses of this mode ONLY work when there is **NO OTHER** dependency added to the build script and the `cas-server-webapp` is the only present module. See [this issue](https://github.com/spring-projects/spring-boot/issues/8320) for more info.


## Spring Boot App Server Selection

There is an app.server property in the `pom.xml` that can be used to select a spring boot application server.
It defaults to `-tomcat` but `-jetty` and `-undertow` are supported.

It can also be set to an empty value (nothing) if you want to deploy CAS to an external application server of your choice.

```xml
<app.server>-tomcat<app.server>
```

## Windows Build

If you are building on windows, try `build.cmd` instead of `build.sh`. Arguments are similar but for usage, run:

```
build.cmd help
```

## External

Deploy resultant `target/cas.war`  to a servlet container of choice.


## Command Line Shell

Invokes the CAS Command Line Shell. For a list of commands either use no arguments or use `-h`. To enter the interactive shell use `-sh`.

```bash
./build.sh cli
```