# MQ-Quad-Debian-Setup

## 一、燒錄 Debian 作業系統
使用 [balenaEtcher](https://www.balena.io/etcher) 將 [Debian (XFCE)](https://mangopi.org/mangopi_mqquad) 燒錄至 SD-card 中。

（預設使用者：root；預設密碼：orangepi）

## 二、首次開機
- 插回 SD-card
- 接上螢幕（[mini HDMI 轉接頭](https://shopee.tw/-%E6%97%A5%E6%9C%AC%E8%B2%93%E9%9B%9C%E8%B2%A8%E8%88%96-(20D231)MiniHDMI-%E8%BD%89HDMI.-Mini-i.31414510.980429457?sp_atk=c3a2b52f-9317-4ab4-b694-9ac2ee7c4e65&xptdk=c3a2b52f-9317-4ab4-b694-9ac2ee7c4e65))
- 接上有線鍵盤 (USB Type-C 轉接頭)
- 接上 USB Type-C 電源

第一次開機會較久，待左上會出現游標，約1分鐘後進入桌面。

### 切換回 Console 並登入
因大多數的操作還是使用 Console 介面來執行比較有效率，所以成功進入桌面後（預設行為），第一件事就是離開桌面回到 Console。

按下：Ctrl + Alt + F3

切換回 Console 並重新登入。

```
login: root
Password: orangepi
```

## 三、設定開機為 Console （不進桌面）
設定開機進 Console：
```
sudo systemctl set-default multi-user.target
```
改回開機進桌面：
```
sudo systemctl set-default graphical.target
```

## 四、使用 nmcli 設定 wifi
下方是常用的 wifi 連線相關指令：

列出 wifi 列表：
```
nmcli device wifi list
```
連線到指定 wifi：
```
nmcli device wifi connect "$SSID" password "$PASSWORD"
```
確認連線狀態：
```
nmcli device status
```
(當 wifi 成功連線後，下次開機會自動連線。)


## 五、查看 wifi 內網 IP
通常使用這種開發板，最合適的操作方式是使用一般的個人電腦用 ssh 遠端連線來控制。（兩者需在同一個無線網路中）

所以首先需要知道當前連線的內網IP位址。

顯示連線資訊：
```
ip a
```
會取得如下訊息：
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 2c:d2:6b:44:0d:68 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.154/24 brd 192.168.0.255 scope global dynamic noprefixroute wlan0
       valid_lft 6206sec preferred_lft 6206sec
    inet6 fe80::2d7b:9389:2362:9443/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
尋找 wlan0 底下的 ***192.168.0.154*** 就是內網 IP

## 六、遠端連線(SSH)
- Mac 使用 Terminal 或 [iTerm2](https://iterm2.com/)。
- Windows 使用 [Putty](https://www.putty.org/) 或安裝 [OpenSSH](https://www.howtogeek.com/311287/how-to-connect-to-an-ssh-server-from-windows-macos-or-linux/) 後使用 PowerShell。

ssh 遠端連線：
```
ssh root@192.168.0.xxx
```
（首次連線需輸入 yes 確認）
再輸入密碼：orangepi 即可進入。

### ssh 問題排除
如果出現 "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!" 代表此內網IP（192.168.0.xxx）先前已被使用過了。此時可以執行下方指令刪除該IP認證紀錄後，再次嘗試ssh連線。
```
ssh-keygen -R 192.168.0.xxx
```
再次ssh連線，輸入yes，再輸入密碼 orangepi：
```
ssh root@192.168.0.xxx
```

## 七、安裝 Node.js
考慮到之後 Node.js 版本的管理問題，比較好的方式是先安裝 Node 的版本管理軟體 nvm，再由他來安裝指定版本。（好處：之後可以安裝多個版本，並在其中隨時切換。）

### 安裝 nvm ([參考](https://www.imaginelinux.com/install-nvm-debian-11/))：
1. 安裝 curl, wget, tar：
```
sudo apt install curl wget tar
```
2. 安裝 nvm：
```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```
3. 重新載入系統指令：
```
source ~/.bashrc
```
4. 顯示 nvm 版本，如顯示版本號碼，則表示有成功安裝。
```
nvm -v
```
### 安裝最新穩定版 Node.js
```
nvm insall node
```
### 其他 nvm 常用指令
|指令|說明|
|---|---|
|nvm ls-remote|列出所有可安裝的版本(很長一串)|
|nvm ls|列出已安裝的版本|
|nvm install {版本號碼}|安裝指定版本|
|nvm install node|安裝最新穩定版|
|nvm use {版本號碼}|切換版本|
|nvm alias default {版本號碼}|將某版本設為預設|

## 八、常用 Linux 指令
|指令|說明|
|---|---|
|sudo reboot|重新開機|
|sudo poweroff|關機｜
|ip a|查看網路連線資訊|
|startx|進桌面|

## 九、VS Code SSH 連線
VS Code 有個方便的外掛，可以使用 ssh 連線到開發板上直接管理、編寫檔案。
1. 安裝 Remote - SSH
1. Command + Shift + P
1. 選擇 Remote-SSH: Connect to Host...
1. 輸入 root@192.168.0.xxx 並再下一步輸入密碼
1. 成功連線後選擇左側 Open Folder (選取指定資料夾後，需再次輸入密碼)