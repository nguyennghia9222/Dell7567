##之前有人让我建群，我懒，直到现在很多人遇到了问题找我，我才发现真是能力越大，责任越大，所以建个交流群吧
![](https://upload-images.jianshu.io/upload_images/16811449-ef82375ff85c3a2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

EFI文件在这里[GitHub](https://github.com/Doapeat/Dell7567)

话不多说，先上配置

| 配置 / Hardware | 详情 / Detail|
| ------ | ------ | 
| 型号 / Model | Dell Inspiron 15 7000 Gaming (7567) |
| 处理器 / CPU | Intel Core i7-7700HQ @ 2.80GHz |
| 内存 / Memory|  16 GB ( DDR4 2400MHz )|
| 硬盘 / HardDrive| Hikvision C2000PRO 1T|
| 显卡 / Graphics Card| Intel HD Graphics 630 (platform-id:0x591B0000) |
| 声卡 / Sound Card | Realtek ALC256 ( layout-id:14 )|
| 网卡 / Network Card | Dell DW1820A ( BCM94350ZAE ) |

## 安装注意事项
* BIOS设置:
	* 关闭 `Legacy Option ROMs`；
	* 设置 `SATA Mode`为 `AHCI` ，自行百度；
	* 关闭 `Secure Boot`；
	* 关闭 `VT ` (最好)；
* `蓝牙驱动:` `BrcmBluetoothInjector.kext` `BrcmFirmwareData.kext` `BrcmPatchRAM2.kext` (3个)；
`WiFi驱动:` `AirportBrcmFixup.kext` (1个)
在`\EFI\Clover\kexts\Off`下，是不会启用的，需要的自行放到`\EFI\Clover\kexts\Other` 注意安装顺序：先从BIOS中关闭WiFi和蓝牙再安装系统，然后安装`蓝牙` `WiFi`驱动；
* `CPU变频驱动:`  SMBIOS机型设置为`（14,3）`的并且CPU是`i7 7700HQ`的不需要，CPU是`i5 7300HQ`的[看这里](https://github.com/stevezhengshiqi/one-key-cpufriend/blob/master/README_CN.md)；

* 安装过程中可能会在剩余2分钟安装完成时自动重启，不用担心，直接选择刚才安装的盘启动；
* 在Clover.plist 中的SMBIOS仿冒机型改为`（14,3）`，原来机型是`（14,1）`的麻烦自己改一下，不修改会导致蓝牙或Wi-Fi驱动失败，需重装才能修复，以后不出大问题都不会换了，原因是`（14,3）`变频良好；
* `CodecCommander.kext` 安装到 `Library\Extensions\`下面可以解决唤醒无声问题，不会安装的可以使用` Kext Utility` 、`Hackintool`等安装；

* 使用ALCPlugFix修复耳机杂音前先运行
```
sudo mount -uw / && killall Finder
```

* 从第三方来源安装程序：
```
sudo spctl --master-disable
```



## 更新日志
* 2020.3.8
	* 使用`VooDooInput.kext`驱动触控板，触控更灵敏，同时修复睡眠唤醒之后偶尔出现触控板无法使用的问题；
	* 合并1080P和4K的Config.plist文件；
	* 注入网卡、显卡、声卡的PCI设备属性，更换DW1820A的请手动去掉`Config.plist/Devices/properties/#PciRoot(0x0)/Pci(0x1C,0x5)/Pci(0x0,0x0)`项目前面的`#`以启用，有助于解决部分搜不到手机热点的问题；
	* 已知问题：
		* 耳机使用`ALCplugFix`+`CodecCommander.kext`可能会导致插入耳机时内置MIC和耳机MIC均无法使用的问题，这是驱动与声卡节点不兼容的原因导致的，`VicQ`修改制作了新的驱动以解决这一问题，还在测试；
		* 添加启动参数`-disablegfxfirmware`可以解决核显最高只能达到400Mhz的问题，自行斟酌使用；

* 2020.2.24
	* 本机型换网卡之后，目前各项功能已趋于完美；
	* 感谢群友`VicQ`，他制作的声卡节点信息已经成功同时驱动了低音单元和前扬声器，已提交`AppleALC`作者审核，不出意外下个版本应该会发布。应`VicQ`要求，暂时不公布测试的`AppALC.kext`文件，静待官方发布；
	* 使用了新的ssdt修复睡眠，并添加启动参数`darkwake=10`，再次感谢群友`VicQ`；
	* 经过查阅资料，反复对比，使用重命名`ECDV to EC`的方式修补EC设备效果最好；
	* 精简重命名规则、驱动和补丁；
	* 已知问题：
		* 7700HQ的显卡几乎只能运行在400MHz左右，极少的时间段下能运行的更高，而7300HQ似乎没有这个问题，希望大家自测；

* 2020.2.2 
	* 没事就多待在家里玩玩黑苹果吧！少出门溜达！
	* 昨天的Clover文件我忘记检查，导致安装失败或者无法进入系统，在此和大家说一句抱歉！今天修复了一下，建议删除原来的文件，仅保留SMBIOS信息，不要覆盖原文件；
	* 在Clover.plist 中的SMBIOS仿冒机型改为`（14,3）`；
	* 修改声卡`LayoutID=14`，成功驱动低音单元，但是低音单元和普通单元是分开的两个输出设备，解决办法详见：[MacOSX多音频设备混合输出方法](https://www.jianshu.com/p/f16e63817bc9)；
	* 添加显卡信息，修改显存为2048M；
	* 重新定制USB，内建蓝牙、摄像头和左侧的USB2.0，方便蓝牙鼠标使用；
	* Lilu.kext、AllpleALC.kext、AirportBrcmFixup.kext、NoTouchID.kext常规升级；

 	* 已知问题：
		* 睡眠时好时坏，具体自测；
* 2020.1.18
	* Clover升级到v5103；
	* 借用了国外大佬`Nihhaar`的VoodooI2C的驱动，非常感谢！！！目前触控板可以驱动，不过没有win10灵敏，具体自测；
	* 增加了4K屏幕的支持，请到`Cover`目录下修改`config_4K.plist`为`config.plist`，具体自测，我的是1080p；
 	* 已知问题：
		* 无法完全睡眠，长时间睡眠时间会睡死；
		* 低音单元尝试了很多办法，还是没有办法驱动；

* 2020.1.1
	* 修复CPU变频，通过CPU-S测得14档左右；

* 2019.12.29
	* 适配10.15.2；
	* 感谢`黑果小兵`提供的镜像；
	* 感谢`kttde`提供的SSDT-LPC.aml和[关于beta4升级beta5卡在AppleNVME Assert Failed的暂时解决办法](http://bbs.pcbeta.com/viewthread-1825003-1-1.html)；
	* 已知问题：
		* CPU变频暂未修复，SMBIOS切换到14,3可以实现自动变频，但是很多大佬都不推荐14,3；


感谢 `aa714` `lijiqiang` [升级到10.15后耳机不能用了](http://bbs.pcbeta.com/viewthread-1828962-1-1.html)
* 2019.5.13
	* 使用VirtualSMC驱动，和FakeSMC相比，很难说哪个更好，可以肯定的是前者更新
	* 使用Hackintool驱动USB，读卡器已驱动
	* 加入`-rad24`参数，强制使用24位色彩，颜色过渡更顺滑，想使用32位的可以去除
	* 使用国际友人的亮度驱动，可以使用【Fn+S】和【Fn+B】进行控制（在此感谢）
	* 由于使用SMCBatteryManager驱动，电池信息不是很完整，有需要的可以换成ACPIBatteryManager

* 2019.5.3
	* 添加AirportBrcmFixup.kext，删除FakePCIID.kext、FakePCIID_BroadcomWiFi.kext，支持重启，支持切换WIFI，支持网卡内建
	* 更新VoodooPS2Controller.kext，触控板支持右键菜单，支持简单手势，需设置，看上图
* 2019.5.2
	* 添加CPUFriend，变频更顺滑，频率更稳定，温度更低
	* 更新NoTouchID，安全鉴定瞬间完成，不用风火轮
	* 精简无用驱动
 	* 驱动更新
 		* Clover_v2.4k r4920
 		* NoTouchID.kext_v1.0.1

* 2019.5.1
 	* 添加并修正applebacklightinjecktor.kext和applebacklight补丁，以修复屏幕最低亮度过亮的Bug
	
* 2019.4.30	
	* Clover更新到Clover_v4903	
	* 新增10.14.x解除USB端口限制补丁
	* 新增DVMT修正补丁
	* 新增NoTouchID驱动
	* 删除applebacklightinjecktor.kext和applebacklight补丁
	* 删除蓝牙驱动，需要的自行驱动吧
	* 驱动更新
		* WhateverGreen_v1.2.8
		* VoodooPS2Controller_v1.9.2
		* Lilu_v1.3.5
		* AppleALC_v1.3.6

* 2018.12.11
	* Clover更新到Clover_v2.4k_r4701.RM-4963.ca6cca7c，来自RehubMan，感谢！
	* 去掉了FakePCIID_Broadcom_WiFi.kext，没换网卡的有这个也没用！
	* 修复了Clover引导界面卡的问题，大家说的Clover界面卡的问题我试了5次，没复现卡的情况，经一个好友反馈，卡顿的情况也不再出现，初步判断是因为用了太花哨的主题，占用资源过多导致

* 2018.12.3
	* 添加了ALCPlugFix，这个补丁是用来修复耳机破音，源自网络 ，里面有用法说明，感谢制作此补丁的大婶！！！

* 2018.11.24
	* 路由器使用了5Ghz，蓝牙正常！宿舍8M内都正常！听了20min不存在断开连接！
	* USBInjectAll.kext 更新到2018-11-08 (0.7.1)，FakePCIID_Broadcom_WiFi.kext 和 FakePCIID.kext更新到2018-1027，来自RehubMan大神，感谢！！！
	* 更新几张图片（有的是上次截的，忘了放在这里面）

* 2018.10.13
	* 首次发布，所有使用的驱动都是当前最新


## 如果你认可我的努力，可以通过 `打赏` 支持我后续的更新
 

|支付宝红包|微信 |支付宝|
| --- | --- | --- |
|![Alipay envelope](https://upload-images.jianshu.io/upload_images/16811449-4176ea54843483f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|![Wechatpay](https://upload-images.jianshu.io/upload_images/16811449-3f1be37277bad3c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|![Alipay](https://upload-images.jianshu.io/upload_images/16811449-7a881429acebdf7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|


## 图片欣赏


![](https://upload-images.jianshu.io/upload_images/16811449-a3a14b199d832ad7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![变频良好](https://upload-images.jianshu.io/upload_images/16811449-2f1c34e1c0004585.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/16811449-0e02c0d48109d649.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![电池](https://upload-images.jianshu.io/upload_images/16811449-ee57efab642c5cfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![声卡](https://upload-images.jianshu.io/upload_images/16811449-aca97b641bd46047.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![显卡](https://upload-images.jianshu.io/upload_images/16811449-54fa2b645f3ffa2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![蓝牙](https://upload-images.jianshu.io/upload_images/16811449-9b85a2fc64560050.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 ![按教程设置就行](https://upload-images.jianshu.io/upload_images/16811449-13ffc1480e4bdc31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![SD读卡器，USB2.0的速度](https://upload-images.jianshu.io/upload_images/16811449-85e8c48f118d240d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 感谢浏览！！！!
