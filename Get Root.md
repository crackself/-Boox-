### Root 前的准备

    设置——更多设置——USB调试模式
    (或下载 App：“隐藏的设置项” 进入 AOSP 系统设置，多次点击设备版本号，打开开发者模式，并且启用 USB 调试)
    数据线接上 PC，装好 ADB 驱动和环境，输入命令：adb devices 查看连接状态
    使用命令 adb reboot bootloader 重启进入 fastboot 模式
    fastboot flashing unlock 检查 bootloader 锁是否解除，文石 Leaf2_P 3.3.1 Android 11 的版本出场 bootloader 就没有上锁

下载固件，提取 boot.img

    [decryptBooxUpdateUpx](https://github.com/Hagb/decryptBooxUpdateUpx)
    [GetBooxUpxKeys](https://github.com/Hagb/GetBooxUpxKeysApp/releases)
    Android 11 需运行GetBooxUpxKeys App 以获取strings.

#### 文石 Bookx Leaf2 分大陆版、海外版

#### Leaf2_P 大陆版strings:

    MODLE="Leaf2_P"
    STRING_SETTINGS="lH1x4y+lRiS8YNYfiDJba3wOcacEOglrIMutctNhpAbl"
    STRING_UPGRADE="6XR9lSuqRSd6Je0RDReTkVSVPHz3KytxeMRgVQvGyxn9"
    fingerprint:Onyx/Leaf2_P/Leaf2_P:11/2022-10-14_17-37-3.3.1_338b46b81/1376:user/dev-keys
    最新固件下载链接:
    http://data.onyx-international.cn/api/firmware/update?where={"buildNumber":0,"buildType":"user","deviceMAC":"","lang":"zh_CN","model":"Leaf2_P","submodel":"","fingerprint":""}

#### Leaf2 海外版（含港澳）strings:

    "MODEL": "Leaf2",
    "STRING_SETTINGS": "okRZqgfOHft/Y5jHx1N0ECjqrc/Op/nADFj2uL7Jbuxq",
    STRING_UPGRADE": "10Na33u5HfsCVHqjtwLrwACOW+2cz5MSnXUogONCTd9c",
    最新固件下载链接:
    http://data.onyx-international.cn/api/firmware/update?where={"buildNumber":0,"buildType":"user","deviceMAC":"","lang":"en_US","model":"Leaf2","submodel":"","fingerprint":""}


### 解压固件文件
    工具:
    decryptBooxUpdateUpx (https://github.com/Hagb/decryptBooxUpdateUpx.git)
    payload-dumper-go   (https://github.com/ssut/payload-dumper-go)

    安装python3 及 pycryptodome
    pip install pycryptodome

    下载decryptBooxUpdateUpx，修改DeBooxUpx.py 添加Leaf2_P string
```
        "STRING_UPGRADE": "kBtW135kW9zvvgRlsTolCktkJ7+MeW5ZoNgr3xyr2SpX",
        "STRING_LOCAL": "jzx1lloJdIBGjmcyS9oYPmXvTKFp4dpj66fPcaON"
    },
+    'Leaf2': {
+        "MODEL": "Leaf2_P",
+        "STRING_SETTINGS": "lH1x4y+lRiS8YNYfiDJba3wOcacEOglrIMutctNhpAbL",
+        "STRING_UPGRADE": "6XR9lSuqRSd6Je0RDReTkVSVPHz3KytxeMRgVQvGyxn9"
+    },
    "Gulliver-ru": {
        "MODEL": "MC_GULLIVER",
        "STRING_SETTINGS": "wDNy4yMayA4nnQfzAYYqV3ih6uu363MoyfhaX9MtbCQk",
```
    解压命令：
    python DeBooxUpx.py <device model> [input file name [output file name]]
    python DeBooxUpx.py Leaf2 update.upx

    通过 payload-dumper-go (https://github.com/ssut/payload-dumper-go) 解压 payload.bin 取得boot.img

    解压命令：
    payload-dumper-go payload.bin


### 使用 Magisk 修补 boot.img

    在Leaf2或任意一台 Android 设备安装好 Magisk APP: https://github.com/topjohnwu/Magisk/releases

    将上文提取出的 boot.img 发送给 Android 设备(链接到PC，通过文件管理器复制)，
    打开手机 Magisk 应用 > 安装 > 选项中只勾选保持系统分区加密 > 选择并修补一个文件 > 选择复制进去的 boot.img > 开始。等待滚动的命令行显示 All Done
    将生成的 magisk_patched-xxxxxx.img 发送给 PC
    
    通过adb-tool
    adb pull /path/to/magisk_patched-xxxxxx.img

### 使用修补过的 boot.img 引导启动文石阅读器

    adb reboot bootloader
    
    文石 Leaf 进入 fastboot 模式
    fastboot boot magisk_patched-xxxxxxx.img
    等待设备重新启动
    
    重启之后进入 magisk，可以看到现在设备已经获得了 root 权限，但这只是临时的
    确认启动引导没有问题后，打开 Magisk App 中选择安装 > 直接安装，永久修补 boot 镜像。

    至此，文石 leaf Root 成功

### 参考链接:
   - https://pppp.ink/blogs/11-wen-shi-boox-leaf-root-xiu-gai-zi-ti.html#_4-%E4%BF%AE%E6%94%B9%E7%B3%BB%E7%BB%9F%E5%AD%97%E4%BD%93
   - https://github.com/Hagb/decryptBooxUpdateUpx
   - https://github.com/Hagb/decryptBooxUpdateUpx/pull/46
   - https://forum.xda-developers.com/t/guide-how-to-extract-img-boot-img-etc-from-payload-bin-using-payload-dumper-go.4468781/
   - https://github.com/ssut/payload-dumper-go

### 所用工具：
   - PC （Win\Linux\MacOS）
   - [Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb?hl=zh-cn)
   - python3
   - pycryptodome
   - decryptBooxUpdateUpx.py
   - payload-dumper-go
   - Magisk APP
   - GetBooxUpxKeys App
