---
title: "Service"
date: 2019-05-28
slug: "service"
menu:
    main:
        weight: -60
        params: 
            icon: cloud
---

目前，除了博客以外，我还有一些公共可用的服务，本页面用于列出这些服务并配上相应的说明。

# Arch 软件源
https://archrepo.viflythink.com

这是我自用的 Arch 软件源，国内外均可高速访问，里面主要包含根据 AUR 的 PKGBUILD 进行打包的软件，质量没有足够的保证，有相关的问题可以在 [GitHub Issues](https://github.com/vifly/arch-build/issues) 中提出。

## 使用方法
首先导入并信任我的签名公钥，如果将来有一天软件包签名失效的话，那有可能是我更换了签名用的密钥，重新执行以下步骤就可以解决：

```
wget -O /tmp/vifly-repo.key 'https://share.viflythink.com/arch-repo.key' && sudo pacman-key --add /tmp/vifly-repo.key
sudo pacman-key --lsign-key viflythink@gmail.com
```

接着就是像添加 archlinuxcn 等第三方软件源一样在 /etc/pacman.conf 末尾添加下面几行：

```
[vifly]
Server = https://archrepo.viflythink.com
```

# Debian 软件源
https://debianrepo.viflythink.com

除了 Arch 软件源外，我还有为 Debian 准备的软件源，同样是国内外均可高速访问，其中包含 fcitx5 百万肥猫词典（中文维基百科包含的词语）、萌娘百科词典等桌面用户所需的软件包，未来也会添加在服务器使用的软件。如有问题可在 [GitHub Issues](https://github.com/vifly/debian-build/issues) 提出。注意，本软件源目前只确保在最新的 Debian 稳定版（stable）可用，如果你使用旧稳定版/测试版/不稳定版 Debian，那么请自行解决报错。

## 使用方法
与使用其它第三方软件源相同，首先导入签名公钥。由于 apt-key 会在 Debian 12 被废弃，所以这里不使用 apt-key 导入公钥。

```
wget -qO - 'https://share.viflythink.com/debian-repo.key' | gpg --dearmor | sudo tee /usr/share/keyrings/vifly-keyring.gpg &> /dev/null
```

接着在 sources.list.d 添加软件源，记得把 bullseye（对应 Debian 11）改为最新的稳定版系统代号（codename）。

```
echo 'deb [signed-by=/usr/share/keyrings/vifly-keyring.gpg] https://debianrepo.viflythink.com/ bullseye main' | sudo tee /etc/apt/sources.list.d/vifly.list
```

# 文件分享
https://share.viflythink.com

还有一个存在感较低的文件分享服务，目前仅用于分发上面所需的签名公钥。

其中的文件都存放在我的服务器上。
