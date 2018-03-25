---
title: Raspberry Pi 3 免鍵盤、滑鼠、螢幕 快速上手
date: 2018-03-25 14:56:17
tags: 
    - raspberry pi
    - headless mode
---
如果沒有要使用Raspberry Pi的桌面環境，安裝還要接上螢幕、鍵盤設定才能從遠端SSH到Raspberry Pi，太麻煩了，本文章將說明當你有了Raspberry Pi，要怎麼使用無頭模式(Headless Mode)安裝，在過程中不需要接上螢幕、滑鼠、鍵盤、網路線到Raspberry Pi上，並透過SSH使用Raspberry Pi。
## 準備設備
Raspberry Pi使用wifi上網則不需要準備網路線，讀卡機是把MicroSD接到電腦上燒入作業系統。
- Raspberry Pi 3
- Raspberry Pi電源線
- 網路線(Optional)
- Micro SD
- Micro SD讀卡機

## 下載OS
下載Raspberry Pi的作業系統到官方網站，我使用的是官方支援的Raspbian作業系統。
[https://www.raspberrypi.org/downloads/](https://www.raspberrypi.org/downloads/)

選擇Raspbian
![raspbian-download](raspbian-download.png)

下載RASPBIAN STRETCH LITE，這個版本是基於Debian Stretch，不包含桌面環境。
![raspbian-download](raspbian-download-stretch.png)

## 燒入映像檔
我使用[Etcher](https://etcher.io/)這個軟體燒入，簡單、快速。

1. 將Micro SD卡插入電腦
2. 打開Etcher軟體
3. 選擇剛剛下載解壓縮後的映像檔
4. 選擇要燒入的Micro SD
5. 按下Flash!

選擇好燒入映像檔與燒入裝置的畫面
![raspbian-download](etcher.png)

正在燒入中
![etcher-flashing](etcher-flashing.png)

## 設定無頭模式(Headless Mode)
### 設定SSH
映像檔預設是不打開SSH的服務，但是我們只要在剛燒入好的Micro SD中`/boot/`資料夾放入一個空檔案取名為`ssh`，就可以開機時預設打開SSH的服務。
```sh
# 切換到boot資料夾
$ cd <Where you mount sd card>/boot
# 新增一個ssh檔案，在windows上可以用筆記本建立一個檔案再放入/boot資料夾中
$ touch ssh
```
### 設定wifi(Optional)
如果使用網路線讓Raspberry Pi上網可以跳過此步驟，設定開機就自動連wifi，需要建立一個檔案取名`wpa_supplicant.conf`一樣放在`/boot/`資料夾下，這次要在檔案中加入下面的內容，替換成wifi名稱與wifi密碼。

wpa_supplicant.conf
```conf
country=TW
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="your_real_wifi_ssid"
    scan_ssid=1
    psk="your_real_password"
    key_mgmt=WPA-PSK
}
```
完成以上步驟後，插入Micro SD卡，如果沒有設定wifi可以連接網路線。
## 連接到Raspberry Pi
Raspberry Pi開機後必須先尋找他的IP位置，可以透過路由器的網頁查看，或著在linux上可以用指令`arp`來尋找區域網路中的裝置，通常新的裝置的IP數字是最大的，這裡我的Raspberry Pi的IP是192.168.1.113。

```sh
$ arp -a
? (192.168.1.113) at b8:27:eb:2c:7d:17 [ether] on enp3s0
? (192.168.1.102) at 00:24:01:a9:f9:eb [ether] on enp3s0
? (192.168.1.110) at e8:cc:18:0e:c9:c5 [ether] on enp3s0
? (192.168.1.103) at 60:45:cb:1b:11:d3 [ether] on enp3s0
? (192.168.1.101) at 70:4f:57:b6:63:1c [ether] on enp3s0
_gateway (192.168.1.1) at 5c:f4:ab:7d:7e:4b [ether] on enp3s0
```
使用SSH連接，預設的使用者是`pi`，密碼是`raspberry`
```sh
$ ssh pi@192.168.1.113
```
連線進去後建議先改密碼與更新系統。
```sh
# 更改密碼
$ passwd
Changing password for pi.
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully

# 更新系統
$ sudo apt update
$ sudo apt upgrade -y

```
開始探索你的raspberry pi吧！

參考資料
[Raspbian Stretch Headless Setup Procedure - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?t=191252)