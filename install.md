
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

## Check GPU 

```bash
nvidia-smi
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
sudo ufw allow 22
sudo ufw reload
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

#### SNA対応の場合

```
openssl genpkey -algorithm RSA -out server.key
openssl req -new -key server.key -out server.csr

- Country Name (C): 国コード (例: JP)
- State or Province Name (ST): 都道府県 (例: Tokyo)
- Locality Name (L): 市区町村 (例: Shinjuku)
- Organization Name (O): 組織名 (例: Example Inc.)
- Organizational Unit Name (OU): 部署名 (例: IT Department)
- Common Name (CN): サーバーのドメイン名 (例: example.com) Krugle AIO Server Private Certification
- Email Address: メールアドレス (任意)

openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt

openssl x509 -in server.crt -text -noout
openssl rsa -in server.key -check
openssl req -in server.csr -text -noout

```

Create subjectnames.txt
EC2の場合、DNS名と、Global IPとPrivate IPをSNAに定義する。
```bash
subjectAltName = DNS:<fqdn>, IP:<Global IP>, IP:<Private IP>

Sample:
subjectAltName = DNS:test.com, DNS:*.example.com, DNS:bar.com, IP:172.17.0.2
```

```
openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt -extfile subjectnames.txt
```

#### Copy cert and key

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

## portproxy (WSL2 on Windows Only)
Create portproxy.bat

```
@echo off
netsh interface portproxy add v4tov4 listenport=443 listenaddress=0.0.0.0 connectport=443 connectaddress=172.22.123.246
netsh interface portproxy add v4tov4 listenport=80 listenaddress=0.0.0.0 connectport=80 connectaddress=172.22.123.246
netsh interface portproxy add v4tov4 listenport=9100 listenaddress=0.0.0.0 connectport=9100 connectaddress=172.22.123.246
netsh interface portproxy add v4tov4 listenport=5000 listenaddress=0.0.0.0 connectport=5000 connectaddress=172.22.123.246
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=8080 connectaddress=172.22.123.246
netsh interface portproxy add v4tov4 listenport=5668 listenaddress=0.0.0.0 connectport=5668 connectaddress=172.22.123.246
```

## Executes portproxy (WSL2 on Windows Only)
```
PS C:\Users\ki-krugle-jp\Krugle> .\portproxy.bat
PS C:\Users\ki-krugle-jp\Krugle> 
```

## Check portproxy (WSL2 on Windows Only)
```
PS C:\Users\ki-krugle-jp\Krugle> netsh interface portproxy show all

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
0.0.0.0         443         172.22.123.246  443
0.0.0.0         80          172.22.123.246  80
0.0.0.0         9100        172.22.123.246  9100
0.0.0.0         5000        172.22.123.246  5000
0.0.0.0         8080        172.22.123.246  8080
0.0.0.0         5668        172.22.123.246  5668

PS C:\Users\ki-krugle-jp\Krugle>
```


## Default User
http://<ip>:8080](http://<Krugleが稼働しているシステム>:8080/install/welcome.html

