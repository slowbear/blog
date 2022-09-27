---
title: 调整Linux字体渲染
author: Slowy
date: 2022-11-05
update: 2022-01-03
#description:
#image:
categories: linux
tags:
  - linux
  - fontconfig
---

> 未完待续

> 首先推荐阅读以下几个文章:
> - [Linux fontconfig 的字体匹配机制](https://catcat.cc/post/2020-10-31/)
> - [用 fontconfig 治理 Linux 中的字体](https://catcat.cc/post/2021-03-07/)
> - [Linux字体美化实战](http://www.jinbuguo.com/gui/linux_fontconfig.html)
> - [fonts.conf 中文手册](http://www.jinbuguo.com/gui/fonts.conf.html)

一直以来我对Windows和Linux下的字体渲染效果不太满意，在离职飞书之后Mac还回去了，日常使用的环境又回到了熟悉的Windows和Linux。由于不少现代工具都使用了emoji和奇奇怪怪的Unicode字符，为了让字体能正确的渲染，我特意学习了一下fontconfig的配置。

目前我的fontconfig配置如下, 一个配置字体fallback顺序，一个用于调整字体渲染:

```xml
<?xml version="1.0"?>
<!-- <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd"> -->
<fontconfig>
    <!-- Config monospace -->
    <match target="pattern">
        <test name="family">
            <string>monospace</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Iosevka SS12</string>
            <string>Noto Sans CJK SC</string>
            <string>Symbols Nerd Font Mono</string>
        </edit>
    </match>
    <!-- Config sans -->
    <match target="pattern">
        <test name="family">
            <string>sans-serif</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Uubuntu</string>
            <string>Noto Sans CJK SC</string>
            <string>Symbols Nerd Font</string>
        </edit>
    </match>
    <!-- Config serif -->
    <match target="pattern">
        <test name="family">
            <string>serif</string>
        </test>
        <edit name="family" mode="prepend" binding="strong">
            <string>Noto Serif CJK SC</string>
            <string>DejaVu Serif</string>
        </edit>
    </match>
</fontconfig>
```

```xml
<?xml version="1.0"?>
<!-- <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd"> -->
<fontconfig>
    <match target="font">
        <edit name="pixelsize">
            <if>
                <less>
                    <name>pixelsize</name>
                    <double>10</double>
                </less>
                <int>10</int>
                <round>
                    <name>pixelsize</name>
                </round>
            </if>
        </edit>
        <edit name="antialias">
            <bool>true</bool>
        </edit>
        <edit name="hinting">
            <bool>true</bool>
        </edit>
        <edit name="autohint">
            <bool>false</bool>
        </edit>
        <edit name="hintstyle">
            <const>hintslight</const>
        </edit>
        <edit name="rgba">
            <const>none</const>
        </edit>
        <edit name="lcdfilter">
            <const>lcddefault</const>
        </edit>
        <edit name="embeddedbitmap">
            <bool>false</bool>
        </edit>
        <edit name="embolden">
            <bool>false</bool>
        </edit>
    </match>
</fontconfig>
```
