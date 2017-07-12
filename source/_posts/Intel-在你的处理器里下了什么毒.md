---
title: Intel 在你的处理器里下了什么毒?
date: 2017-07-12 19:03:15
tags: 自由软件 硬件 翻译 非自由软件 CPU 后门
thumbnail: https://duangsuse.gitlab.io/blog/img/thumb/libreboot.png
categories: 硬件
---
记得之前在LibreBoot(开源高效的(EFI)BIOS 替代品) 的介绍中看到了这段描述,不由感慨他们研究的用心.现将其翻译成文,放在博客上.希望大家能对非自由软件的"良苦用心"有更多了解.

`注:部分内容翻译以后容易出现理解偏差,本文采取英汉对照逐句翻译.`
>Intel Management Engine (ME)

`英特尔管理引擎` (ME)
>Introduced in June 2006 in Intel’s 965 Express Chipset Family of (Graphics and) Memory Controller Hubs, or (G)MCHs, and the ICH8 I/O Controller Family, the Intel Management Engine (ME) is a separate computing environment physically located in the (G)MCH chip.

因特尔管理引擎(ME)是一个__物理上处于(G)MCH芯片中__,`与主机完全独立`的操作环境.*2006年6月*,它于因特尔的_965高速芯片组_(包含(图形和)内存控制中心,或(G)MCH和ICH8族的I/O控制器)中被加入.
>In Q3 2009, the first generation of Intel Core i3/i5/i7 (Nehalem) CPUs and the 5 Series Chipset family of Platform Controller Hubs, or PCHs, brought a more tightly integrated ME (now at version 6.0) inside the PCH chip, which itself replaced the ICH. Thus, the ME is present on all Intel desktop, mobile (laptop), and server systems since mid 2006.

*2009年的Q3*,第一代`英特尔酷睿 i3/i5/i7(Nehalem)` 处理器和5个系列的芯片组,包含平台控制中心,或PCH,`带来了一个安装在PCH芯片内部的,更集成得更顽固的ME(现在已经6.0版了)`.它自己就替代了ICH.因此,__ME在所有2006年中以后的英特尔桌面,手机(笔记本平板)和服务器上都存在__.
>The ME consists of an ARC processor core (replaced with other processor cores in later generations of the ME), code and data caches, a timer, and a secure internal bus to which additional devices are connected, including a cryptography engine, internal ROM and RAM, memory controllers, and a direct memory access (DMA) engine to access the host operating system’s memory as well as to reserve a region of protected external memory to supplement the ME’s limited internal RAM.

ME包含一个__ARC处理器核心__(在更新的版本中拿其它处理核心替换了),__代码,和数据缓存__,__一个计时器__,__一个秘密的内部总线来分辨有那些连上的外部设备__,还包含一个加密引擎,内部ROM和RAM,内存控制器,`以及用于直接访问主机操作系统的内存和接收一块被保护的外部内存来支持ME`~~大小不敢恭维~~`的内部内存的直接内存访问引擎(DMA)`.
>The ME also has network access with its own MAC address through an Intel Gigabit Ethernet Controller.

ME通过因特尔吉字节宽带控制器,`拥有自己的MAC地址和网络访问能力`
>Its boot program, stored on the internal ROM, loads a firmware “manifest” from the PC’s SPI flash chip.

它的引导程序被储存在内部的只读存储器上,引导程序会从PC内部的SPI闪存芯片上读取一个固件 __"清单"__.
>This manifest is signed with a strong cryptographic key, which differs between versions of the ME firmware.

`这个清单被强大的加密算法密钥签名了,而且每个版本的ME固件都有不同的密钥`.
>If the manifest isn’t signed by a specific Intel key, the boot ROM won’t load and execute the firmware and the ME processor core will be halted.

`如果这个清单不是由专门的因特尔密钥签名的话,引导器就不会加载引导ROM并执行固件,ME处理器就会被关闭`.
>The ME firmware is compressed and consists of modules that are listed in the manifest along with secure cryptographic hashes of their contents.

ME固件被压缩,__并且包括了一些列在清单里的,包含秘密哈希的模组__
>One module is the operating system kernel, which is based on a proprietary real-time operating system (RTOS) kernel called “ThreadX”. 

其中一个模组就是操作系统的内核,这个内核基于一个叫做*"ThreadX"*的__专有__实时操作系统(RTOS)内核.
>The developer, Express Logic, sells licenses and source code for ThreadX.

*Express Logic*是这个内核的开发者,它__出售这个ThreadX内核的许可证和源代码__.
>Customers such as Intel are forbidden from disclosing or sublicensing the ThreadX source code.

像因特尔这样的客户被`禁止停止闭源`或为*ThreadX*再做授权的做法
>Another module is the Dynamic Application Loader (DAL), which consists of a Java virtual machine and set of preinstalled Java classes for cryptography, secure storage, etc. The DAL module can load and execute additional ME modules from the PC’s HDD or SSD.

另外一个模组是__动态应用程序加载器(DAL)__,它包含了一个__Java虚拟机(JVM)__和`一打用于密码学,秘密存储,杂项的预装的Java类.DAL模组可以加载和执行其它从PC的固态/机械硬盘中可选的ME模组`.
>The ME firmware also includes a number of native application modules within its flash memory space, including Intel Active Management Technology (AMT), an implementation of a Trusted Platform Module (TPM), Intel Boot Guard, and audio and video [DRM](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%89%88%E6%9D%83%E7%AE%A1%E7%90%86#.E6.95.B0.E5.AD.97.E7.89.88.E6.9D.83.E7.AE.A1.E7.90.86.E7.9A.84.E5.8F.8D.E5.AF.B9.E8.80.85) systems.

ME固件还包含了`不少不在其内部空间里的原生应用程序模组`,包括__英特尔活动管理技术(AMT)__,一个"信任平台模组([TPM](https://zh.wikipedia.org/wiki/%E5%8F%AF%E4%BF%A1%E8%AE%A1%E7%AE%97))"的实现,及__英特尔引导守卫__,和音/视频[DRM](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%89%88%E6%9D%83%E7%AE%A1%E7%90%86#.E6.95.B0.E5.AD.97.E7.89.88.E6.9D.83.E7.AE.A1.E7.90.86.E7.9A.84.E5.8F.8D.E5.AF.B9.E8.80.85)系统.
>The Active Management Technology (AMT) application, part of the Intel “vPro” brand, is a Web server and application code that enables remote users to power on, power off, view information about, and otherwise manage the PC. 

*活动管理技术(AMT)*应用程序,英特尔"vPro"商标的一部分,是一个`允许远程用户打开/关闭,查询信息,或者反正就是怎么管理电脑的网络服务器和应用程序代码`
>It can be used remotely even while the PC is powered off (via Wake-on-Lan).

它可以远程使用,__即使计算机已经关机也行__(通过局域网唤醒)
>Traffic is encrypted using SSL/TLS libraries, but recall that all of the major SSL/TLS implementations have had highly publicized vulnerabilities.

`数据交换已经通过SSL/TLS库加密了,但记住所有主要的SSL/TLS实现已经有很大程度上公开的漏洞了`.
>The AMT application itself has known vulnerabilities, which have been exploited to develop rootkits and keyloggers and covertly gain encrypted access to the management features of a PC.

AMT应用程序本身就__有为人所知的漏洞了__,并且这些漏洞已经被利用来开发__rootkit__和__键盘记录器__,及__便捷的获取加密的会话来管控一个计算机的特性__
>Remember that the ME has full access to the PC’s RAM.

记住,ME可对计算机的*内存*拥有__完全__的访问权限.
>This means that an attacker exploiting any of these vulnerabilities may gain access to everything on the PC as it runs: all open files, all running applications, all keys pressed, and more.

这意味着,一个攻击者可以随意利用以上`一切漏洞`,而这可能让他获取到计算机上运行的`一切东西的访问权限`:
+ 所有__打开在内存里的文件__
+ 所有__在运行的应用程序__
+ 你按下的__所有键__
+ ....还有很多....

>Intel Boot Guard is an ME application introduced in Q2 2013 with ME firmware version 9.0 on 4th Generation Intel Core i3/i5/i7 (Haswell) CPUs. 

*英特尔引导守卫*是*Q2 2013年*,在英特尔酷睿i3/i5/i7(Haswell),ME版本9.0上加入的ME应用程序
>It allows a PC OEM to generate an asymmetric cryptographic keypair, install the public key in the CPU, and prevent the CPU from executing boot firmware that isn’t signed with their private key.

它允许PC的OEM生成密码学不对称的密钥,在CPU中安装公钥,并且`阻止CPU执行非他们(OEM)私钥签名`~~钦定~~`的引导固件`
>This means that coreboot and libreboot are impossible to port to such PCs, without the OEM’s private signing key. Note that systems assembled from separately purchased mainboard and CPU parts are unaffected, since the vendor of the mainboard (on which the boot firmware is stored) can’t possibly affect the public key stored on the CPU.

这意味着Coreboot和Libreboot在没有OEM的签名私钥的情况下__不可能__移植到那些PC上.注意,_独立购买主板和CPU部件组装的系统不受影响_，因为主板的供应商（引导固件存储的地方）不可能影响存储在CPU上的公钥。
>ME firmware versions 4.0 and later (Intel 4 Series and later chipsets) include an ME application for audio and video [DRM](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%89%88%E6%9D%83%E7%AE%A1%E7%90%86#.E6.95.B0.E5.AD.97.E7.89.88.E6.9D.83.E7.AE.A1.E7.90.86.E7.9A.84.E5.8F.8D.E5.AF.B9.E8.80.85) called “Protected Audio Video Path” (PAVP).

ME固件版本4.0及以后(英特尔4系列及以后的芯片组) 包含了一个叫做"*PVAP*"的,为音/视频进行数字限制管理(__DRM__)的ME应用.
>The ME receives from the host operating system an encrypted media stream and encrypted key, decrypts the key, and sends the encrypted media decrypted key to the GPU, which then decrypts the media.

ME从主机操作系统那接收`被加密`的媒体流和`被加密`的密钥,然后,它便`解密这个密钥`,然后便把`加密的媒体`和`解密过的密钥`发送到GPU,`GPU最后解密这个媒体`.
>PAVP is also used by another ME application to draw an authentication PIN pad directly onto the screen.

PAVP也被另外一个ME应用程序用来将用与验证的PIN面板__直接绘制在屏幕上__
>In this usage, the PAVP application directly controls the graphics that appear on the PC’s screen in a way that the host OS cannot detect.

由于~~有这种操作~~这种用途,__PAVP应用程序可以以一种主机操作系统检测不到的方式直接控制在PC监视器上显示的图像__
>ME firmware version 7.0 on PCHs with 2nd Generation Intel Core i3/i5/i7 (Sandy Bridge) CPUs replaces PAVP with a similar [DRM](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%89%88%E6%9D%83%E7%AE%A1%E7%90%86#.E6.95.B0.E5.AD.97.E7.89.88.E6.9D.83.E7.AE.A1.E7.90.86.E7.9A.84.E5.8F.8D.E5.AF.B9.E8.80.85) application called “Intel Insider”. 

ME固件7.0,`处于每一个英特尔酷睿二世代i3/i5/i7(Sandy Bridge)`CPU所匹配的PCH上,已经以一个叫做"*Intel Insider*"的,差不多的*DRM*应用程序替代了*PAVP*
>Like the AMT application, these DRM applications, which in themselves are defective by design, demonstrate the omnipotent capabilities of the ME: this hardware and its proprietary firmware can access and control everything that is in RAM and even everything that is shown on the screen.

像AMT应用程序一样,这些[DRM](https://zh.wikipedia.org/wiki/%E6%95%B0%E5%AD%97%E7%89%88%E6%9D%83%E7%AE%A1%E7%90%86#.E6.95.B0.E5.AD.97.E7.89.88.E6.9D.83.E7.AE.A1.E7.90.86.E7.9A.84.E5.8F.8D.E5.AF.B9.E8.80.85)应用程序,自己在[设计上就有缺陷](https://defectivebydesign.org/), 展示了它可怕的全能: 这种硬件和其私有固件可以访问和控制计算机RAM中的,甚至是显示的所有东西.
>The Intel Management Engine with its proprietary firmware has complete access to and control over the PC: it can power on or shut down the PC, read all open files, examine all running applications, track all keys pressed and mouse movements, and even capture or display images on the screen.

英特尔管理引擎和它的私有固件拥有PC上完全的访问权和控制权:
+ 它可以随便将您的PC开/关机
+ 它可以随便读取打开在内存中的文件,__包括已解密的文件__
+ 它可以__知道有那些应用程序正在运行__,*并能对这些应用做更多的事*
+ 它可以__跟踪您按下的键/鼠标的移动__
+ 它还可以__在您无法知道的情况下进行截图__,或者__在监视器上随意显示图像__

>And it has a network interface that is demonstrably insecure, which can allow an attacker on the network to inject rootkits that completely compromise the PC and can report to the attacker all activities performed on the PC. 

并且它的网络接口__非常的不安全__,可以让网络上的攻击者几乎_100%_成功注入rootkit到PC上,然后攻击者可以知道这台计算机上进行的*所有*活动
>It is a threat to freedom, security, and privacy that can’t be ignored.

__它对自由,安全和隐私有很大的威胁,这不能被无视.__
>Before version 6.0 (that is, on systems from 2008/2009 and earlier), the ME can be disabled by setting a couple of values in the SPI flash memory. 

在ME固件6.0之前(就是说 2008/2009年或更早的系统上),_ME可以简单的通过在SPI闪存里设置一对值来禁用_
>The ME firmware can then be removed entirely from the flash memory space.

然后,__ME固件就可以完全从闪存内存空间中移除__了
>libreboot does this on the Intel 4 Series systems that it supports, such as the Libreboot X200 and Libreboot T400. ME firmware versions 6.0 and later, which are found on all systems with an Intel Core i3/i5/i7 CPU and a PCH, include “ME Ignition” firmware that performs some hardware initialization and power management.

在英特尔4系列的系统中,~~由于有这种操作~~libreboot就这样做了,比如在*Libreboot X200*和*Libreboot T400*上.可是,在ME固件6.0版本及以后,所有使用英特尔酷睿 i3/i5/i7 CPU和PCH的系统上,包含了一个叫"*ME Ignition*"的固件来进行一些_硬件件初始化_和_电源管理_.
>If the ME’s boot ROM does not find in the SPI flash memory an ME firmware manifest with a valid Intel signature, the whole PC will shut down after 30 minutes.

如果ME的引导ROM不能在SPI闪存内存中找到一个`带有有效英特尔签名的ME固件清单`,__整个计算机将在30分钟后关机__.
>Due to the signature verification, developing free replacement firmware for the ME is basically impossible.

由于这个签名验证,为ME开发自由的固件替代品,__几乎就是不可能的__.
>The only entity capable of replacing the ME firmware is Intel.

真正_有能力替换ME固件能力的_,__只有英特尔自己__.
>As previously stated, the ME firmware includes proprietary code licensed from third parties, so Intel couldn’t release the source code even if they wanted to.

就像之前说的,ME固件包含由第三方授权的私有代码,__所以即使英特尔想要开放源代码也不行__.
>And even if they developed completely new ME firmware without third-party proprietary code and released its source code, the ME’s boot ROM would reject any modified firmware that isn’t signed by Intel.

并且,即使他们开发一个全新的,__不带第三方私有代码的ME固件并且将其开源,ME的引导ROM还是会拒绝所有非英特尔签名的,修改了的固件__.
>Thus, the ME firmware is both hopelessly proprietary and “tivoized”.

因此,ME固件是__没有任何改变希望的私有软件__,而且还__被__[Tivo](http://www.gnu.org/proprietary/proprietary-tyrants.html)__化了__
>In summary, the Intel Management Engine and its applications are a backdoor with total access to and control over the rest of the PC. 

总的来说,英特尔管理引擎和它的应用程序,就是__对PC其它部分拥有完全访问和控制权限的后门__
>he ME is a threat to freedom, security, and privacy, and the libreboot project strongly recommends avoiding it entirely. Since recent versions of it can’t be removed, this means avoiding all recent generations of Intel hardware.

这个ME啊~~,excited!~~,是_自由_,_安全_和_隐私_的__极大威胁__.并且libreboot项目强力推荐完全避免它.自最近的,ME无法被移除的版本以来,这就是说应该`避开所有最近世代的英特尔硬件`.
>More information about the Management Engine can be found on various Web sites, including me.bios.io, unhuffme, coreboot wiki, and Wikipedia.

关于ME的更多信息可以在许多网站上找到,包括 me.bios.io , unhaffme , coreboot wiki 和 维基 这些网站.
>The book Platform Embedded Security Technology Revealed describes in great detail the ME’s hardware architecture and firmware application modules.

一本叫"_Platform Embedded Security Technology Revealed_"描写了ME硬件,固件,应用模组架构的一大堆细节.
>If you’re stuck with the ME (non-libreboot system), you might find this interesting: http://hardenedlinux.org/firmware/2016/11/17/neutralize_ME_firmware_on_sandybridge_and_ivybridge.html

如果你必须使用ME(非libreboot系统),你可能能觉得[这个](http://hardenedlinux.org/firmware/2016/11/17/neutralize_ME_firmware_on_sandybridge_and_ivybridge.html)很有用
>Also see (effort to disable the ME): https://www.coreboot.org/pipermail/coreboot/2016-November/082331.html - look at the whole thread

你也可以看看[努力禁用ME](https://www.coreboot.org/pipermail/coreboot/2016-November/082331.html) -看完整篇文章

以上内容均是我手动逐句翻译的~~其中有一个句子是去让百度翻译了修改的,你们猜是哪句?~~ 如果你想喷我翻译的不好的话,我完全可以说:"你初中毕业了给我翻译一个啊",你们又不高兴,说我年少轻狂,naive,那我该怎么办?欢迎指正,不过请保持正常交流,不要成天搞事情,要不然[出门右转](http://translate.baidu.com/#auto/zh/),让你们瞎下眼![看看,翻译得...](http://cdn.rkidc.loveml.com/uploads/1707122339D6947F45F.png),你们识得不识得啊?#(滑稽)

Intel的行为和M$有着惊人的相似:
![Windos10](http://static.fsf.org/nosvn/windows/win_infographic_final.png)
