# 使用JRE本地keystore Android证书
Android平台打包发布apk应用，需要使用数字证书（.keystore文件）进行签名，用于表明开发者身份。

Android证书的生成是自助和免费的，不需要审批或付费。

可以使用JRE环境中的keytool命令生成。以下是windows平台生成证书的方法：
## jre环境
从Oracle官方下载jre安装包：`https://www.oracle.com/java/technologies/downloads/#java8 `
向系统环境中添加jre的路径变量(按实际位置)
```
C:\Program Files\Java\jre\bin"
```
## 生成证书
在想要存储的位置打开cmd命令行，输入
```
keytool -genkey -alias testalias -keyalg RSA -keysize 2048 -validity 36500 -keystore test.keystore
```
其中 testalias为证书名，test.keystore为密钥库名，两者可自定

## 查看证书
在密钥库位置打开cmd命令行，输入
```
keytool -list -v -keystore test.keystore  
```
按提示输入密钥库密码后，可查看证书信息