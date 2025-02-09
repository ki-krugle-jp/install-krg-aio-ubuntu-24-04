
# Driver

https://docs.nvidia.com/cuda/cuda-installation-guide-linux/

https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=24.04&target_type=deb_network



## CUDA Toolkit Installer
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-8

```
##  Driver Installer

### Install Open Kernel Module
```bash
sudo apt-get install -y nvidia-open
```


## Reboot the Machine

```bash
sudo systemctl reboot
```

## Download AIO Image

Define username(dluser) and password(dlpass) for download site.

```bash
dluser=""
dlpass=""
```



https://downloads.krugle.org/enterprise/

```bash
wget --user=${dluser}  --password=${dlpass} https://downloads.krugle.org/enterprise/Krugle-AiO-installer-enterprise-0.6.4-20250118T111026.tgz
wget --user=${dluser}  --password=${dlpass} https://downloads.krugle.org/enterprise/Krugle-AiO-installer-enterprise-0.6.4-20250118T111026.tgz.md5sum.txt
```

## Check md5 checksum
```bash
md5sum -c Krugle-AiO-installer-enterprise-0.6.4-20250118T111026.tgz.md5sum.txt
```

## Mandatory Packages
```bash
sudo apt install -y ufw
```

Enable & Start ufw service
```bash
sudo systemctl enable ufw
sudo systemctl start ufw
sudo ufw --force enable
```

## Extract AIO installer tarball
```bash
tar xvzf Krugle-AiO-installer-enterprise-0.6.4-20250118T111026.tgz
```

## TLS Certificate 

Krugleの使用にはHTTPSが必要です。

PEM形式の証明書ファイルkrugle-aio-installer/krugle/kng/kse/ssl/server.crtおよびキー ファイルkrugle-aio-installer/krugle/kng/kse/ssl/server.keyを編集/置換して、独自の証明書とキーにしてください。

### Generate Private Cert

```
openssl genpkey -algorithm RSA -out server.key -aes256 -pass pass:yourpassword
openssl genpkey -algorithm RSA -out server.key
openssl req -new -key server.key -out server.csr

- Country Name (C): 国コード (例: JP)
- State or Province Name (ST): 都道府県 (例: Tokyo)
- Locality Name (L): 市区町村 (例: Shinjuku)
- Organization Name (O): 組織名 (例: Example Inc.)
- Organizational Unit Name (OU): 部署名 (例: IT Department)
- Common Name (CN): サーバーのドメイン名 (例: example.com)
- Email Address: メールアドレス (任意)

openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt

openssl x509 -in server.crt -text -noout
openssl rsa -in server.key -check
openssl req -in server.csr -text -noout


```

```bash
ls krugle-aio-installer
cp -v server.crt krugle-aio-installer/krugle/kng/kse/ssl/server.crt
cp -v server.key krugle-aio-installer/krugle/kng/kse/ssl/server.key
```

## Executes AIO Installer

```bash

cd krugle-aio-installer
sudo ./install.sh

```

## Default User
http://<ip>:8080](http://<Krugleが稼働しているシステム>:8080/install/welcome.html

