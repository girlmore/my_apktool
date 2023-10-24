# APK重新打包签名
apktool下载地址:
https://ibotpeaches.github.io/Apktool/
## 1.解压apk包
java -jar apktool_2.9.0.jar d app-release.apk  
$ apktool d -s -f test.apk  
-d 反编译 apk 文件  
-s 不反编译 dex 文件，而是将其保留  
-r 参数不会反编译资源文件  
-f 如果目标文件夹存在，则删除后重新反编译
## 2.删除签名文件
签名文件在解压文件后的\app-release\META-INF目录下 C:\Users\xxoo\Downloads\app-release\META-INF
## 3.添加要替换的文件到
C:\Users\xxoo\Downloads\app-release\assets下
## 4.生成签名文件
keytool -genkeypair -alias cert -keyalg RSA -validity 20000 -keystore xxoo.keystore -storepass 123456  
* 一次性生成：keytool -genkeypair -alias mykey -keypass 123456 -keyalg RSA -keysize 2048 -validity 20000 -keystore  xxoo.keystore -storepass 123456 -dname "CN=名字与姓氏,OU=组织 单位名称,O=组织名称,L=城市或区域名称,ST=州或省份名称,C=单位 的两字母国家代码"
查看签名信息  
* 查看APK签名信息：keytool-printcert -file META-INF/CERT.RSA
## 5.重新打包
java -jar apktool_2.9.0.jar b app-release  
$apktool b b_test -o newtest.apk  
-b 是指 build  
-o 用于指定新的文件名称，这里指定为「newtest.apk」  
b_test 是刚才反编译出的文件所在的目录  
## 6.使用重新打包后的apk和签名文件打包
jarsigner -verbose -keystore xxoo.keystore -storepass 123456 -signedjar app-release-signed.apk app-release.apk cert  
或者  
java -jar apksigner.jar sign -v --ks xxoo.keystore --ks-key-alias cert --ks-pass pass:123456 --key-pass pass:123456 --in in.apk --out out_signed.apk
## 7.验证 APK 签名
在受支持的平台上确认 APK 签名是否成功通过验证的语法如下：  
apksigner verify [options] app-name.apk  
apksigner verify -v --print-certs C:\app-name.apk  
使用 apksigner verify 校验已签名的 apk 文件。包括查看签名方式和使用的证书信息。  
经常使用参数有：  
-v 显示签名详情，是否使用 v1 、v2 签名。  
--in 指定待校验的 apk 文件路径。当 apk 路径放在命令末尾时，此参数能够省略。  
--print-certs 显示 apk 文件中包含的签名文件证书信息。  
* 示例 1  
apksigner verify --in app-release.apk -v -print-certs
* 示例 2  
apksigner verify -v -print-certs app-release.apk
## 8.字节对齐
* 如果您使用的是 apksigner，则必须在为 APK 文件签名之前使用 zipalign。如果您在使用 apksigner 为 APK 签名之后对 APK 做出了进一步更改，签名便会失效。
* 如果您使用的是 jarsigner（不推荐），则必须在为 APK 文件签名之后使用 zipalign。  
zipalign -p -f -v 4 infile.apk outfile.apk  
zipalign -c -v 4 outfile.apk
