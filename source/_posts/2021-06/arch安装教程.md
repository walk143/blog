---
title: arch安装教程
date: 2021-05-30 21:38:11
tags:
categories:
---

介绍arch的安装及部分配置
<!-- more -->

# 准备

## 下载镜像

[download](https://archlinux.org/download/)

可直接选择`http://mirrors.163.com/archlinux/iso/2021.03.01/`中下载`http://mirrors.163.com/archlinux/iso/2021.03.01/archlinux-2021.03.01-x86_64.iso`

## 写入U盘

U盘使用[rufus](https://rufus.ie/downloads/)

可使用便携版，后缀带`p`

`uefi`安装方式，此处的分区类型应选择`GPT`。

   {% asset_img 1615627360216.png 1615627360216 %}

## 引导

设置bios选择U盘启动

# 安装

## 准备

选择默认，进入安装模式

{% asset_img 1615627465656.png 1615627465656 %}

{% asset_img 1615627516331.png 1615627516331 %}

## 检查引导

```sh
ls /sys/firmware/efi/efivars
ls: cannot access '/sys/firmware/efi/efivars': No such file or directory
#表明你是以BIOS方式引导，否则为以EFI方式引导。
```

## 临时连网

> 暂时使用手机网络进行联网

1. 手机连接usb，开启usb共享网络
2. `sudo ip link` 展示所有网卡
3. `sudo ip link set enp0s*** up` 打开对应的网卡
4. `sudo dhcpcd enp0s***` 给对应的网卡分配网络


## 分区

1. 检查分区

	```sh
	fdisk -l
	```
	
	{% asset_img 1615627909923.png 1615627909923 %}

	{% asset_img 1615627987945.png 1615627987945 %}

2. 格式化

   ```sh
   mkfs.ext4 /dev/sdxY （请将的sdxY替换为刚创建的分区）
   mkfs.vfat /dev/sdxZ # 此分区为boot引导分区，需要单独分盘。500M即可
   ```

   {% asset_img 1615628081423.png 1615628081423 %}

   

3. 挂载分区

   ```sh
   mount /dev/sdxY /mnt （请将sdxY替换为之前创建的根分区）
   ```

4. **如果你是EFI/GPT引导方式**，执行以下命令创建/boot文件夹并将引导分区挂载到上面。**BIOS/MBR引导方式无需进行这步。**

   ```
   mkdir /mnt/boot
   mount /dev/sdxZ /mnt/boot （请将sdxY替换为之前创建或是已经存在的引导分区）
   ```


5. 选择镜像源

   ```sh
   vim /etc/pacman.d/mirrorlist
   Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
   Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
   ```

6. 安装基本包

   ```sh
   pacstrap /mnt base base-devel linux linux-firmware dhcpcd
   ```

7. 配置Fstab

   ```sh
   genfstab -L /mnt >> /mnt/etc/fstab
   cat /mnt/etc/fstab
   ```

8. 切换到arch

   ```sh
   arch-chroot /mnt
   ```
	 
## 安装驱动

### 声卡驱动

```sh
sudo pacman -S alsa-utils pulseaudio-alsa
```

### 触摸板驱动

```sh
sudo pacman -S xf86-input-libinput
# 输出系统中的设备
libinput list-devices
# 查看设备并确定其名称和编号
xinput list
# 自定义配置
vim /etc/X11/xorg.conf.d/30-touchpad.conf
```

### 安装显卡驱动

```sh
lspci -v | grep -A1 -e VGA -e 3D
# 或
lspci | grep VGA
```

{% asset_img img.png img %}

```sh
sudo pacman -S xf86-video
```

## 引导

1. 设置时区

   ```sh
   ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   hwclock --systohc
   ```

3. 提前安装必须软件包

   ```sh
   pacman -S vim dialog wpa_supplicant ntfs-3g networkmanager
   ```

4. 设置Locale

   ```sh
   vim /etc/locale.gen
   #在文件中找到zh_CN.UTF-8 UTF-8 zh_HK.UTF-8 UTF-8 zh_TW.UTF-8 UTF-8 en_US.UTF-8 UTF-8这四行，去掉行首的#号，保存并退出
   locale-gen
   vim /etc/locale.conf
   LANG=en_US.UTF-8
   # end
   
   ```

5. 设置主机名

   ```sh
   vim /etc/hostname
	 # start
   nickerg
	 # end
   vim /etc/hosts
	 # start
   127.0.0.1	localhost
   ::1		localhost
   127.0.1.1	nickerg.localdomain	nickerg
	 # end
   
   ```

6. 设置Root密码

   ```sh
   passwd
   ```

   
7. 安装Bootloader

   1. bios引导(未测试)
   
        ```sh
         pacman -S os-prober ntfs-3g
         pacman -S grub
         # 此不可用
         grub-install --target=i386-pc /dev/sdx （将sdx换成你安装的硬盘）
         # 使用此
         grub-install --recheck /dev/sda
         grub-mkconfig -o /boot/grub/grub.cfg
        ```
   
   2. 如果为EFI/GPT引导方式：

       - 安装`grub`与`efibootmgr`两个包：

         ```
         pacman -S grub efibootmgr
         ```

       - 部署`grub`：

         ```
         grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
         ```

       - 生成配置文件：

         ```
         grub-mkconfig -o /boot/grub/grub.cfg
         ```


8. 安装后检查

   ```sh
   vim /boot/grub/grub.cfg
   ```

   检查接近末尾的`menuentry`部分是否有`windows`或其他系统名入口。

9. 重启

    ```sh
    exit
    # umount /mnt/boot
    umount /mnt
    reboot
    ```

# 配置

## 联网

```sh
pacman -S networkmanager
ip link set enp0s3 up
dhcpcd enp0s3
```

### 连接无线

#### 安装nmcli

```sh
sudo pacman -S networkmanager
sudo systemctl enable NetworkManager.service
sudo systemctl start NetworkManager.service
```

#### 使用nmcli

```sh
ip addr set wlan0 up
```

List nearby Wi-Fi networks:

```
$ nmcli device wifi list
```

Connect to a Wi-Fi network:

```
$ nmcli device wifi connect SSID_or_BSSID password "password"
```

Get a list of connections with their names, UUIDs, types and backing devices:

```
$ nmcli connection show
```

Activate a connection (i.e. connect to a network with an existing profile):

```
$ nmcli connection up name_or_uuid
```

Delete a connection:

```
$ nmcli connection delete name_or_uuid
```

See a list of network devices and their state:

```
$ nmcli device
```

Turn off Wi-Fi:

```
$ nmcli radio wifi off
```

### 设置静态ip

```sh
nmcli connection show
NAME                UUID                                  TYPE      DEVICE
FAST_BAB8           77f677e0-50e5-40f7-8cee-3d26ffa19eda  wifi      wlp4s0
br-51b4ca580679     3e3ff898-0b66-448c-80ec-00c99b017ba6  bridge    br-51b4ca580679
br-c607db215989     12c40db2-ce1a-46aa-b3a9-48517e701558  bridge    br-c607db215989
docker0             607b8b85-6dcf-45c2-ab3e-2c645295dd01  bridge    docker0
Wired connection 1  6d4ee391-901f-35c6-bea8-c825d027820d  ethernet  --

nmcli c mod FAST_BAB8 ipv4.address 192.168.0.109/24 ipv4.gateway 192.168.0.1 ipv4.dns 192.168.0.1 ipv4.method manual
ifconfig
nmcli c down FAST_BAB8
Connection 'FAST_BAB8' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/8)
nmcli c up FAST_BAB8
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/9)
```

## 添加`archlinuxcn`源

在 `/etc/pacman.conf` 文件末尾添加以下两行：

```
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

然后安装 GPG key

```
sudo pacman -Syu
sudo pacman -S archlinuxcn-keyring
```


## 添加用户

```sh
useradd -m -G wheel -s /bin/bash nickerg
passwd nickerg
ln -s /usr/bin/vim /usr/bin/vi
visudo
# 取消注释
wheel ALL=(ALL) ALL
#end

su nickerg
```

## 配置aur

安装yay

```
sudo pacman -S yay
```

修改`aururl`

```
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```

## zsh

```sh
yay -S zsh
chsh -s /bin/zsh
yay -S oh-my-zsh-git
yay -S zsh-syntax-highlighting zsh-autosuggestions autojump
sudo ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting /usr/share/oh-my-zsh/custom/plugins/
sudo ln -s /usr/share/zsh/plugins/zsh-autosuggestions /usr/share/oh-my-zsh/custom/plugins/
chsh -s /bin/zsh
# 重启即可生效
```

## openssh

```sh
yay -S openssh
sudo vim /etc/ssh/sshd_config
PermitRootLogin yes
Port 6000
ListenAddress 0.0.0.0
# end
sudo systemctl enable sshd.service
sudo systemctl start sshd.service
```

### 配置ssh

```sh
chmod 600 *
chmod 644 *.pub
chmod 644 config

vim ~/.ssh/config
# start
Host walk143
  User walk143
  HostName  github.com
  PreferredAuthentications publickey
  IdentityFile D:\Softs\PortableGit\.ssh\sloera_qq_rsa
Host nickerg
  User nickerg
  HostName  github.com
  PreferredAuthentications publickey
  IdentityFile D:\Softs\PortableGit\.ssh\nickerg_inspur_rsa
Host gitee.com #git@gitee.com:sloera/sloera.git中的gitee.com
  User sloera
  HostName  gitee.com
  PreferredAuthentications publickey
IdentityFile D:\Softs\PortableGit\.ssh\gitee_163_rsa
Host git.inspur.com
  HostName  git.inspur.com
  PreferredAuthentications publickey
  IdentityFile D:\Softs\PortableGit\.ssh\sloeraN_gitlab
Host walk #git@gitee.com:sloera/sloera.git中的gitee.com
  User sloera
  HostName  gitee.com
  PreferredAuthentications publickey
IdentityFile D:\Softs\PortableGit\.ssh\gitee_qq_rsa

Host 192.168.0.108
  HostName 192.168.0.108
  User root
# end

```

vim替换

```sh
:%s/D\:\\Softs\\PortableGit\\.ssh\\/\/home\/sloera\/\.ssh\//g
```

> 注意：`\`不需要转义，`/`,`.`需要转义

### 初始化仓库

```sh
[nickerg@~]$ git init
[nickerg@~]$ git remote add origin git@gitee.com:sloera/arch-i3.git
[nickerg@~]$ rm -rf .bash_profile .bashrc .bash_logout
[nickerg@~]$ git pull origin master
# 修改
[nickerg@~]$ git config --global user.name sloera
[nickerg@~]$ git config --global user.email sloera@163.comn
[nickerg@~]$ git commit -am "modify bash prompt"
On branch master
nothing to commit, working tree clean
```

## 安装桌面所需软件

```sh
yay -S neofetch alacritty polybar dmenu ranger tmux rofi campton variety feh variety i3-gaps
sudo pacman -S xorg-server
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
sudo systemctl start lightdm
# 如果启动失败，尝试重启系统
yay -S i3exit
```
### 

```sh
sudo vim /etc/default/grub
# start
#GRUB_CMDLINE_LINUX_DEFAULT="quiet sqlash acpi_backlight=vendor"
GRUB_CMDLINE_LINUX_DEFAULT="quiet acpi_osi=! acpi_osi=\"Windows 2009\""
sudo update-grub
sudo apt install xbacklight xorg xserver-xorg-video-intel
```

## zsh

```sh
yay -S zsh
chsh -s /bin/zsh
yay -S oh-my-zsh-git
yay -S zsh-syntax-highlighting zsh-autosuggestions autojump
sudo ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting /usr/share/oh-my-zsh/custom/plugins/
sudo ln -s /usr/share/zsh/plugins/zsh-autosuggestions /usr/share/oh-my-zsh/custom/plugins/
yay -S alacritty
```

## 调整分辨率

> https://wiki.archlinux.org/index.php/Xrandr_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

```sh
sudo pacman -S xorg-xrandr
$ xrandr --output HDMI-1 --mode 1920x1080 --rate 60
/etc/X11/xorg.conf
Section "Monitor"
    Identifier      "External DVI"
    Modeline        "1280x1024_60.00"  108.88  1280 1360 1496 1712  1024 1025 1028 1060  -HSync +Vsync
    Option          "PreferredMode" "1280x1024_60.00"
EndSection
Section "Device"
    Identifier      "ATI Technologies, Inc. M22 [Radeon Mobility M300]"
    Driver          "ati"
    Option          "Monitor-DVI-0" "External DVI"
EndSection
Section "Screen"
    Identifier      "Primary Screen"
    Device          "ATI Technologies, Inc. M22 [Radeon Mobility M300]"
    DefaultDepth    24
    SubSection "Display"
        Depth           24
        Modes   "1280x1024" "1024x768" "640x480"
    EndSubSection
EndSection

Section "ServerLayout"
        Identifier      "Default Layout"
        Screen          "Primary Screen"
EndSection
```

## virtualbox

### arch 上安装 virtualbox

> https://wiki.archlinux.org/title/VirtualBox_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

```sh
sudo pacman -S virtualbox-host-modules-arch
# 手动加载模块
# modprobe vboxdrv
# modprobe vboxnetadp
# modprobe vboxnetflt
# modprobe vboxpci
# sudo vboxreload
sudo pacman -S net-tools
sudo pacman -S virtualbox-guest-iso
# 安装扩展包
yay -S virtualbox-ext-oracle
sudo usermod -G vboxusers -a 用户名
# /usr/lib/virtualbox/additions/VBoxGuestAdditions.iso
# win 交换坐ctrl和caps
# https://blog.csdn.net/Orange_5Lee/article/details/109602588

VirtualBox
```

### 安装virtualBox Guest Additions增强

> https://wiki.archlinux.org/index.php/VirtualBox/Install_Arch_Linux_as_a_guest

```sh
sudo pacman -Q virtualbox-guest-utils
# 添加共享文件夹
sudo mount -t vboxsf tfs /media/tfs

```

#### 自动挂载

> https://www.javaroad.cn/questions/361529

```sh
sudo pacman -Q virtualbox-guest-utils
sudo systemctl enable vboxservice.service
sudo systemctl start vboxservice.service
# 重新启动
```

#### 自动挂载没有权限

```sh
sudo usermod -aG vboxsf $(whoami)
```

## 日常


### 字体

> https://wiki.archlinux.org/title/Fonts_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E4%B8%AD%E6%97%A5%E9%9F%A9%E8%B6%8A%E6%96%87%E5%AD%97

```sh
sudo pacman -S ttf-font-awesome wqy-bitmapfont wqy-microhei wqy-microhei wqy-zenhei nerd-fonts-complete noto-fonts-cjk
vim .zshrc
# can't display chinese
# start
export LANG=en_US.utf8
# end
```

### 交换坐ctrl和caps

```sh
sudo vim /etc/profile.d/swap.sh
setxkbmap -option ctrl:swapcaps
. /etc/profile
```

```sh
xmodmap -pke > ~/.xmodmap
vim .xmodmap
# 修改按键
# 使用 xeV 查看按键
xmodmap ~/.xmodmap
```



### 挂载 ntfs 分区

- 安装`ntfs-3g`

`sudo pacman -S ntfs-3g`

#### 手动挂载

```sh
mount /dev/you_NTFS_partion /mount/point
或
ntfs-3g /dev/your_NTFS_partition /mount/point
```

示例

```sh
sudo mount /dev/nvme0n1p5 /mnt/soft
```

#### 自动挂载

```sh
sudo vim /etc/fstab
[sloera@127 ~]$ cat /etc/fstab 
# Static information about the filesystems.
# See fstab(5) for details.

# <file system> <dir> <type> <options> <dump> <pass>
# /dev/sdb2 UUID=8120937f-d921-44d3-be01-123ea1f97636
/dev/sdb2           	/         	ext4      	rw,relatime	0 1

# /dev/sdb1 UUID=795D-373D
/dev/sdb1           	/boot     	vfat      	rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro	0 2

# /dev/sdb3 UUID=ba5c873c-b418-4df3-99f0-3ddc52979a2d
/dev/sdb4           	/home     	ext4      	rw,relatime	0 2

/swapfile           	none     	swap      	defaults	0 0


/dev/sda2           	/mnt/joy     	ntfs-3g      	defaults	0 0


sudo mount -a
```

### neovim

#### node
```sh
sudo pacman -S nodejs
```

```sh
yay -S neovim
nvim
:PlugInstall

sudo pacman -S go xclip
cd .config/nvim/plugged/vim-hexokinase
make hexokinase

:checkhealth
```
- [x] install pynvim (pip); `yay -S python-pip && pip install pynvim`
- [x] install nodejs, and do `sudo npm install -g neovim && sudo npm -g install instant-markdown-d  `


### lazygit

```sh
yay -S lazygit
```

### keepass

```sh
# sudo pacman -S keepassxc # can't use webdav
# sudo pacman -S keepass
# yay -S keepass
# 配置
https://dav.jianguoyun.com/dav/WebDev/KeePass/android.kdbx
# 中文乱码问题
[sloera@127 ~]$ sudo vim /usr/bin/keepass 
[sloera@127 ~]$ cat /usr/bin/keepass 
#!/bin/sh
exec mono --verify-all /usr/share/keepass/KeePass.exe "$@"
export LANG=zh_CN.utf8

```

### 壁纸

```sh
yay -S feh
feh --bg-center /home/sloera/Downloads/wallpaper.jpg
```

### 连接安卓手机

> http://blog.lujun9972.win/blog/2018/03/09/%E8%BF%9E%E6%8E%A5android%E6%89%8B%E6%9C%BA%E5%88%B0archlinux%E4%B8%8A/index.html

```sh
# 启用mtp支持
sudo pacman -S mtpfs --noconfirm
yay -S jmtpfs
# 手动挂载
jmtpfs ~/mnt
# 自动挂载
sudo pacman -Sy gvfs-mtp
```



### frp

https://hub.fastgit.org/fatedier/frp

服务端

```ini
[common]
bind_port = 7000
dashboard_port = 7500
token = 1
dashboard_user = admin
dashboard_pwd = 1
vhost_http_port = 6081
vhost_https_port = 6082
```

客户端

```ini
[common]
server_addr = 49.233.254.141
server_port = 7000
token = ddddd

#公网通过ssh访问内部服务器
[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 6000
remote_port = 6002
```

#### 配置为systemctl服务

1. frpc.sh

   ```sh
   #!/bin/bash
   ### BEGIN INIT INFO
   name=frpc
   case "$1" in
       start)
           `nohup /home/local/git/frp/frp_0.35.1_linux_amd64/frpc -c /home/local/git/frp/frp_0.35.1_linux_amd64/frpc.ini > /home/local/git/frp/frp_0.35.1_linux_amd64/nohup.log 2>&1 &`
           ;;
       stop)
           #`ps -ef | grep frpc | grep -v grep | awk '{print $2}' | xargs kill -9`
           `ps -ef | grep  frpc | grep -v grep | awk '{print "kill -9 " $2}'|sh`
           echo "kill the server "${name}
           ;;
       restart)
           $0 stop
           $0 start
           ;;
       *)
           echo "Usage: $0 {start|stop|restart}"
           exit 1
           ;;
   esac
   
   ```

2. frpc.service

   ```service
   Requires=
   
   [Service]
   ExecStart=/home/local/git/frp/frp_0.35.1_linux_amd64/frpc.sh start
   ExecStop=/home/local/git/frp/frp_0.35.1_linux_amd64/frpc.sh stop
   ExecReload=/home/local/git/frp/frp_0.35.1_linux_amd64/frpc.sh restart
   Type=forking
   
   [Install]
   WantedBy=multi-user.target
   
   ```

3. 复制

   ```sh
   cp frpc.service /etc/systemd/system
   systemctl daemon-reload
   systemctl start frpc.service
   ```

### 剪贴板管理

```sh
yay -S copyq
```

### 截图

```sh
sudo pacman -S flameshot
```

### [通讯](通讯)
#### wechat

```sh
sudo vim /etc/pacman.conf
# 打开 multilab的配置
sudo pacman -Syy
sudo pacman -S wine-for-wechat
sudo systemctl restart systemd-binfmt
yay -S com.qq.weixin.deepin     #基于deepin wine5的wechat
sudo pacman -S p7zip
mkdir -p ~/.deepinwine/deepin-wine-helper
cd ~/.deepinwine/deepin-wine-helper
7z x /opt/apps/com.qq.weixin.deepin/files/helper_archive.7z
mkdir -p ~/.deepinwine/deepin-wine6-stable
7z x /opt/apps/com.qq.weixin.deepin/files/wine_archive.7z
```

#### qq
```sh
# yay -S com.qq.im.deepin
yay -S deepin.com.qq.im.light
```
#### tim
> https://github.com/countstarlight/deepin-wine-tim-arch#%E5%88%87%E6%8D%A2%E5%88%B0-deepin-wine

```sh
yay -S deepin-wine-tim
cd /opt/apps/com.qq.office.deepin/files
# 切换为deep-wine
./run.sh -d
# 修改配置
./run.sh winecfg
```

### floccus

浏览器书签同步

### thunderbird

```sh
yay -S thunderbird-extension-enigmail
sudo pacman -S thunderbird
```
#### 添加账号

-  add ons 安装 ExQuilla for Exchange 插件
-  accountsettings 添加 microsoft exchange account
-  填写用户名、密码
-  选择 login with email address
-  使用 auto discover: 
-  如失败，手动填写。 EWS URL 为：https://mail.temp.com/EWS/Exchange.asmx 。 将mail.temp.com 换成具体的邮箱

#### 加密

- account settings
- manage identities
- edit
- end to end encryption
- 配置 s/MIME 证书

### mpv

```sh
sudo pacman -S mpv
```

### rdp win远程桌面连接

```sh
sudo pacman -S freerdp
# xfreerdp /v:192.168.1.123
xfreerdp /v:192.168.1.123:3389 /u:administrator /p:password /w:1920 /h:1050 /sound
```
### typora

```sh
sudo pacman -S typora
```
### sunlogin 远程 向日葵
```sh
yay -S sunlogin
sudo systemctl start runsunloginclient
# 为使Sunlogin控制在用户登录前及注销后出现的SDDM界面，执行以下命令：
sudo sh -c 'echo "/usr/bin/xhost +" >> /usr/share/sddm/scripts/Xsetup'
```

### goldendict

> https://www.cnblogs.com/keatonlao/p/12702571.html

## 开发

### easyconect

> https://blog.csdn.net/u010563350/article/details/105135881
>
> https://www.wannaexpresso.com/2020/06/07/easy-connect-manjaro/

```[sh](sh)
cd /opt
git clone https://aur.archlinux.org/easyconnect.git
cd easyconnect
vim PKGBUILD
# 替换 
source= 
("http://download.sangfor.com.cn/download/product/sslvpn/pkg/linux_767/EasyConnect_x64_7_6_7_3.deb"
        "http://ftp.acc.umu.se/pub/GNOME/sources/pango/1.42/pango-1.42.4.tar.xz")
md5sums=('ac2020ce44583d5ee4552c81563dce9c'
          'deb171a31a3ad76342d5195a1b5bbc7c')
# 为

source=("http://download.sangfor.com.cn/download/product/sslvpn/pkg/linux_01/EasyConnect_x64.deb"
        "http://ftp.acc.umu.se/pub/GNOME/sources/pango/1.42/pango-1.42.4.tar.xz")
md5sums=('6ed6273f7754454f19835a456ee263e3'
          'deb171a31a3ad76342d5195a1b5bbc7c')
# 安装
makepkg
makepkg --install
# 更新依赖库
curl -JOL http://ftp.acc.umu.se/pub/GNOME/sources/pango/1.42/pango-1.42.4.tar.xz
tar xf pango-1.42.4.tar.xz
cd pango-1.42.4
./configure --prefix=/usr
make && make DESTDIR="/home/sloera/oldlib/pango" install

sudo vim /usr/share/applications/EasyConnect.desktop
# 粘贴下面的内容，并注释原有内容
Exec=env LD_LIBRARY_PATH=/home/sloera/oldlib/pango/usr/lib /usr/share/sangfor/EasyConnect/EasyConnect --enable-transparent-visuals --disable-gpu

# 在终端中运行
cd /usr/share/sangfor/EasyConnect
# 在终端运行EasyConnect
./EasyConnect

```

登陆后小图标闪动数秒后闪退，是由于svpnservice没有正常启动。

登陆时，当登陆进度条运行至70%左右，在终端回车运行命令。

```sh
sudo /usr/share/sangfor/EasyConnect/resources/shell/sslservice.sh
```

### snap

> https://cn.ubuntu.com/blog/what-is-snap-application
>
> https://snapcraft.io/install/datagrip/arch

```sh
yay -S snapd
sudo systemctl enable --now snapd.socket
sudo ln -s /var/lib/snapd/snap /snap

```

#### lol
```sh
sudo snap install leagueoflegends --edge
```


### WPS

  ```
  yay -S wps-office ttf-wps-fonts
  ```

### libreoffice
```sh
sudo pacman -S libreoffice
```
### 蒲公英

```sh
yay -S  pgyvpn-bin
```

### postman

  ```
  sudo pacman -S postman-bin
  ```
### jetbrains

  > https://www.debugpoint.com/2021/02/install-java-arch/

  专业版

  ```
  yay -S jetbrains-toolbox
  ```
	
#### svn
```sh
sudo pacman -S svn
```
svn 无法保存密码问题

一、
vim ~/.subversion/config
用vim修改以下四个地方
```conf
store-passwords = yes
store-plaintext-passwords = yes
store-ssl-client-cert-pp = yes
store-ssl-client-cert-pp-plaintext = yes
```

二、
`sudo chmod -R 777 ~/.subversion/auth`

#### jdk

## 系统运维

### 磁盘监测

```sh
sudo pacman -S sysstat
```

### 双屏显示



# 参考

> 1. https://wiki.archlinux.org/title/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
