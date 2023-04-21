# alpine-bbrplus  
Alpine Kernel USE BBRplus, ported from BBRplus 4.14

### 感谢
基于 cx9208 和 UJX6N 的 bbrplus patch  
https://github.com/cx9208/bbrplus  
https://github.com/UJX6N/bbrplus-5.15  

### 编译步骤
设置git
```
git config --global user.name "nivrrex"
git config --global user.email "nivrrex@gmail.com"
```

git clone 源码
```
cd ~
git clone https://gitlab.alpinelinux.org/alpine/aports
cd aports
git checkout origin/3.17-stable
```
打 bbrplus 补丁
```
cd main/linux-lts
wget https://raw.githubusercontent.com/UJX6N/bbrplus-5.15/main/convert_official_linux-5.15.x_src_to_bbrplus.patch -O bbrplus.patch
sed -i '/awk.patch/abbrplus.patch' APKBUILD
sed -i -e $'s/bbrplus.patch/\tbbrplus.patch/g' APKBUILD
sed -i '/CONFIG_TCP_CONG_BBRPLUS=m/d' lts.x86_64.config
sed -i '/CONFIG_TCP_CONG_BBRPLUS=m/d' virt.x86_64.config
sed -i '/CONFIG_TCP_CONG_BBR=m/aCONFIG_TCP_CONG_BBRPLUS=m' lts.x86_64.config
sed -i '/CONFIG_TCP_CONG_BBR=m/aCONFIG_TCP_CONG_BBRPLUS=m' virt.x86_64.config
```
开始编译， "-F" 是强制使用 root 进行编译，普通用户可以不加
```
abuild -F deps
abuild-keygen -a -n
abuild -F checksum
abuild -F -r
```

### FAQ
安装时出现 ERROR: UNTRUSTED signature 错误，是因为签名问题，可以通过以下方式之一解决：  
1. 将 release 中下载的 key.rsa.pub 复制到 /etc/apk/keys/ 文件夹中
2. 安装时使用 --allow-untrusted 参数 apk add linux-virt-5.15.107-r0.apk --allow-untrusted  
