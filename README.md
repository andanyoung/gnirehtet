# Gnirehtet (v2.5)

This project provides **reverse tethering** over `adb` for Android: it
allows devices to use the internet connection of the computer they are plugged
on. It does not require any _root_ access (neither on the device nor on the
computer). It works on _GNU/Linux_, _Windows_ and _Mac OS_.

Currently, it relays [TCP] and [UDP] over [IPv4] traffic, but it does not
support [IPv6] (yet?).

[TCP]: https://en.wikipedia.org/wiki/Transmission_Control_Protocol
[UDP]: https://fr.wikipedia.org/wiki/User_Datagram_Protocol
[IPv4]: https://en.wikipedia.org/wiki/IPv4
[IPv6]: https://en.wikipedia.org/wiki/IPv6


## Flavors

Two implementations of _Gnirehtet_ are available:
 - one in **Java**;
 - one in **Rust**.


### Which one to choose?

Use the **Rust** implementation. The native binary consumes less CPU and memory,
and does not require a _Java_ runtime environment.

The relay server of _Gnirehtet_ was initially only implemented in Java. As a
benefit, the same "binary" runs on every platform having _Java 8_ runtime
installed. It is still maintained to provide a working alternative in case of
problems with the Rust version.


## Requirements

The Android application requires at least API 21 (Android 5.0).

For the _Java_ version only, _Java 8_ (JRE) is required on your computer. On
Debian-based distros, install the package `openjdk-8-jre`.

### adb

You need a recent version of [adb] (where `adb reverse` is implemented, it
works with 1.0.36).

It is available in the [Android SDK platform tools][platform-tools].

On Debian-based distros, you can alternatively install the package
`android-tools-adb`.

On Windows, if you need `adb` only for this application, just download the
[platform-tools][platform-tools-windows] and extract the following files to the
_gnirehtet_ directory:
 - `adb.exe`
 - `AdbWinApi.dll`
 - `AdbWinUsbApi.dll`

Make sure you [enabled adb debugging][enable-adb] on your device(s).

[adb]: https://developer.android.com/studio/command-line/adb.html
[enable-adb]: https://developer.android.com/studio/command-line/adb.html#Enabling
[platform-tools]: https://developer.android.com/studio/releases/platform-tools.html
[platform-tools-windows]: https://dl.google.com/android/repository/platform-tools-latest-windows.zip


## Get the app

### Homebrew

If you use [Homebrew](https://brew.sh/), getting up and running is very quick.
To install the Rust version:

```
brew install gnirehtet
```

### Download

Download the [latest release][latest] in the flavor you want.

[latest]: https://github.com/Genymobile/gnirehtet/releases/latest


#### Rust

 - **Linux:** [`gnirehtet-rust-linux64-v2.5.zip`][direct-rust-linux64]  
   (SHA-256: _2b1ce04540e8de5df5ddbebb64bb01e27c13d556b3a04a8563dcce3786765705_)
 - **Windows:** [`gnirehtet-rust-win64-v2.5.zip`][direct-rust-win64]  
   (SHA-256: _9f6d7700368f45d2fa43923324660eca9f879e837e10fc45d8d975273eae4755_)
 - **MacOS:** [`gnirehtet-rust-macos64-v2.2.1.zip`][direct-rust-macos64]
   _(old release)_  
   (SHA-256: _902103e6497f995e1e9b92421be212559950cca4a8b557e1f0403769aee06fc8_)

[direct-rust-linux64]: https://github.com/Genymobile/gnirehtet/releases/download/v2.5/gnirehtet-rust-linux64-v2.5.zip
[direct-rust-win64]: https://github.com/Genymobile/gnirehtet/releases/download/v2.5/gnirehtet-rust-win64-v2.5.zip
[direct-rust-macos64]: https://github.com/Genymobile/gnirehtet/releases/download/v2.2.1/gnirehtet-rust-macos64-v2.2.1.zip

Then extract it.

The Linux and MacOS archives contain:
 - `gnirehtet.apk`
 - `gnirehtet`

The Windows archive contains:
 - `gnirehtet.apk`
 - `gnirehtet.exe`
 - `gnirehtet-run.cmd`


#### Java

 - **All platforms:** [`gnirehtet-java-v2.5.zip`][direct-java]  
   (SHA-256: _c65fc1a35e6b169ab6aa45e695c043e933f6fd650363aea7c2add0ecb0db27ca_)

[direct-java]: https://github.com/Genymobile/gnirehtet/releases/download/v2.5/gnirehtet-java-v2.5.zip

Then extract it. The archive contains:
 - `gnirehtet.apk`
 - `gnirehtet.jar`
 - `gnirehtet`
 - `gnirehtet.cmd`
 - `gnirehtet-run.cmd`


## Run (simple)

_Note: On Windows, replace `./gnirehtet` by `gnirehtet` in the following
commands._

The application has no UI, and is intended to be controlled from the computer
only.

If you want to activate reverse tethering for exactly one device, just execute:

    ./gnirehtet run

Reverse tethering remains active until you press _Ctrl+C_.

On Windows, for convenience, you can double-click on `gnirehtet-run.cmd`
instead (it just executes `gnirehtet run`, without requiring to open a
terminal).

The very first start should open a popup to request permission:

![request](assets/request.jpg)

A "key" logo appears in the status bar whenever _Gnirehtet_ is active:

![key](assets/key.png)

Alternatively, you can enable reverse tethering for all connected devices
(present and future) by calling:

    ./gnirehtet autorun


## Run

You can execute the actions separately (it may be useful if you want to reverse
tether several devices simultaneously).

Start the relay server and keep it open:

    ./gnirehtet relay

Install the `apk` on your Android device:

    ./gnirehtet install [serial]

In another terminal, for each client, execute:

    ./gnirehtet start [serial]

To stop a client:

    ./gnirehtet stop [serial]

To reset the tunnel (useful to get the connection back when a device is
unplugged and plugged back while gnirehtet is active):

    ./gnirehtet tunnel [serial]

The _serial_ parameter is required only if `adb devices` outputs more than one
device.

For advanced options, call `./gnirehtet` without arguments to get more details.


## Run manually

The `gnirehtet` program exposes a simple command-line interface that executes
lower-level commands. You can call them manually instead.

To start the relay server:

    ./gnirehtet relay

To install the apk:

    adb install -r gnirehtet.apk

To start a client:

    adb reverse localabstract:gnirehtet tcp:31416
    adb shell am start -a com.genymobile.gnirehtet.START \
        -n com.genymobile.gnirehtet/.GnirehtetActivity

To stop a client:

    adb shell am start -a com.genymobile.gnirehtet.STOP \
        -n com.genymobile.gnirehtet/.GnirehtetActivity


## Environment variables

`ADB` defines a custom path to the `adb` executable:

```bash
ADB=/path/to/my/adb ./gnirehtet run
```

`GNIREHTET_APK` defines a custom path to `gnirehtet.apk`:

```bash
GNIREHTET_APK=/usr/share/gnirehtet/gnirehtet.apk ./gnirehtet run
```


## Why _gnirehtet_?

    rev <<< tethering

(in _Bash_)


## Developers

Read the [developers page].

[developers page]: DEVELOP.md


## Licence

    Copyright (C) 2017 Genymobile

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.


## Articles

- [Introducing “gnirehtet”, a reverse tethering tool for Android][medium-1] ([French version][blog-1])
- [Gnirehtet 2: our reverse tethering tool for Android now available in Rust][medium-2]
- [Gnirehtet rewritten in Rust][blog-2-en] ([French version][blog-2-fr])

[medium-1]: https://medium.com/@rom1v/gnirehtet-reverse-tethering-android-2afacdbdaec7
[blog-1]: https://blog.rom1v.com/2017/03/gnirehtet/
[medium-2]: https://medium.com/genymobile/gnirehtet-2-our-reverse-tethering-tool-for-android-now-available-in-rust-999960483d5a
[blog-2-en]: https://blog.rom1v.com/2017/09/gnirehtet-rewritten-in-rust/
[blog-2-fr]: https://blog.rom1v.com/2017/09/gnirehtet-reecrit-en-rust/


最近我发现了一款开源软件 Gnirehtet，作者是 GitHub 上的 Genymobile。

软件在电脑端运行之后可以用数据线共享电脑网络，这样手机无需连接 WiFi 无需数据流量，一样可以高速上网。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/ncicWtGoBHtLia4C5mKp9Mv8AKnZMWIOggZxqMGTHp2LHnRerGeHJx0N8zSkO7v0XxVnTOyQKFc4FAQ7MbuC8bgw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

它的原理就是通过 adb 为 Android 设备提供 reverse tethering（反向网络连接），允许设备在插入计算机后，通过建立一个虚拟专用网络（VPN），使用电脑的网络而不用消耗自身的流量。

它有一些使用限制条件，只能是安卓手机，还要求是安卓 5.1 以上系统，当然无需 root，软件支持 Windows 系统，但是不支持 Windows XP 系统。

其实这些限制基本上对大部分用户来说都不是问题，现在就教大家如何来设置。

**设置方法**

在手机设置中打开开发者选项，并勾选允许 USB 调试。

如果手机设置找不到开发者选项的打开方式，那就说明之前还没开启过，由于各个牌子手机开启的方式不同，大家简单去搜一下就能解决。

![图片](https://mmbiz.qpic.cn/mmbiz_png/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73Qjly6OYo9HlVTxX5BJUa57KqloiaicHdrSkFLnpxcDticRZ3TRpYHU4ocg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在用数据线连接电脑时选择传输文件。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73QbhQWDlBMHVtTibxoAmJCQLibiau71r3qZroVlpHrCDxfq5icIe6vziaLic1w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在后台获取电脑端的软件，解压后打开 ，双击打开 gnirehtet-run 启动软件。

![图片](https://mmbiz.qpic.cn/mmbiz_png/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73Q9STMeA7xutq0kl0souqAlBmQnDVRF0IswAI2QPxm53p2KHbVHkdM3g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这时电脑会有一个黑色命令窗口弹出。如果出现以下界面说明连接成功了。

![图片](https://mmbiz.qpic.cn/mmbiz_png/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73QJXEreBXcVu4m7MTdxkboAloianTJGukCYtpXD1fuAObS9uJ0j33LqEg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

手机端也会出现以下提示，分别点击继续安装和确定。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73Qd7Fxd1nWzKW88B7SaVrF97Dd1MpX6LzVWxOPMCpakryYVzIdojtiafg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在手机上开启飞行模式测试一下，确实不需要数据流量也可以正常上网了。

不过使用时也不要不要关闭电脑上的命令窗口。

想要断开连接时，在手机上通知栏中点击 Stop Gnirehtet 就可以了。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/ncicWtGoBHtLNwicr6t4cvEH1y63PKv73Q0enoF6nZMicyeGONjQVia4y35RHpmNwos1zVgw3g9UrjO2BtISib3syeg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如此以来，终于让手机可以享用高速上网了。

为了检验速度，我在家里的网络环境下进行了测速比较，用这个方法比之前的连 WiFi 时的速度还快了不少。

![图片](https://mmbiz.qpic.cn/mmbiz_png/ncicWtGoBHtLia4C5mKp9Mv8AKnZMWIOggq3NDoNGa5cBn9zFX7zT20SsKawl07Hm8gbKiaISIH2icSBJJTI2DRB1A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

最近这两天不是一直在玩《使命召唤》嘛，昨晚还和小伙伴们开黑，玩网游的话一定得保证网速稳定，大家可以试试看用这种方式连接上网，速度还是非常给力的。

**结语**
