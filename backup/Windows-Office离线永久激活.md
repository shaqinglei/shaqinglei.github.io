## 重磅发布：Windows/Office被国外大神破解，全部离线永久激活！



上周五国外破解团队MASSGRAVE成功将Windows7至Windows11以及Office 2013至2024的所有版本全部破解，目前均可离线永久激活，为近几十年来最成功的破解，原作者将项目TSForge进行了开源。看到这个新闻后，第一时间对激活机制进行了探究，发现基本原理就是「电话激活」，但又有所不同。

![图片](https://mmbiz.qpic.cn/mmbiz_png/pxopGibm8O53aUTs787AQ3KS6nxIYTDmgur9U15HZt3E5eXv3m6gPdMyrJhFj3TQW2eAbLVMDZt0rd1Y14oGjtQ/640?wx_fmt=png&from=appmsg)

正常来说，微软为了让那些无法联网的用户，通过拨打客服电话进行激活Windows或Office，那么需要给微软提供一组安装ID，这组ID是和电脑硬件以及密钥相关的，微软收到这组ID后，返回一组确认ID（即CID），用户在电脑上手动输入CID即可完美离线激活。那么从安装ID转化为CID的过程，是需要一套加密算法的，微软自己当然知道，如果想要得到CID，除非将这套算法破解。  

破解这套算法的难度极大，迄今为止，仅Windows XP的电话激活的加密算法被完全攻克(可以直接计算出CID)，而此次TSForge并非是破解了电话激活的这套算法。那么它和电话激活有什么关系呢？

经常备份激活信息的小伙伴都知道，微软会把激活相关的数据（比如硬件HWID、CID等等）存储到Store文件夹里面（data.dat、tokens.dat）。这些操作都是通过微软软件保护平台（SPP）实现的，SPP负责保护和管理软件许可证，验证微软产品的合法性，防止未经授权的复制、篡改许可和启动功能。  

MASSGRAVE团队在研究时发现，SPP将激活数据写入上述文件，需要一系列的加密、解密算法，一旦写入激活数据后即认为已经激活，且后续不再进行检查，被称作受信任的存储（Trusted Store）。加密解密只能通过SPP实现，所以作者不但需要搞清楚存储数据时加密、解密、签名检查和哈希检查这些过程，还需要搞清楚RSA私钥，经过探索addition-chain exponentiation运算，掌握了整个过程并得到了从Windows Vista到Windows 11的所有SPP的完整RSA密钥。

因此，作者拿到了所有的RSA私钥，则可以让SPP API往data.dat、tokens.dat等文件里存储激活数据，即便是伪造的数据依然可以存储进去，作者直接伪造了一组CID，这组数值由多个0组成。

![图片](https://mmbiz.qpic.cn/mmbiz_png/pxopGibm8O53aUTs787AQ3KS6nxIYTDmgKtVnoSOBn5h3iaP7cCVuR7rgOlnwO35oAVgUaEjwwHp6Da8xdYnMl6Q/640?wx_fmt=png&from=appmsg)

当然了，除了存储CID之外，作者还存储了激活数据相关的HWID（这个用了一组适用于所有硬件的ID）等数据，所以使用这个方法激活后，即便是更换硬件，也不会影响激活状态。而对于真正使用微软提供的电话激活后，更改主要硬件后，激活状态也会被取消。

通过上面的简介，其实原作者就是伪造（Forge）了一些数据（Trusted Store），所以这种方法命名为TSForge。并且，大家可以看到，该激活方法并未修改任何Windows组件，和真正的电话激活一样，往存储数据的文件中写入了一些假数据而已。因此，该方案非常安全，没有任何副作用，激活后也不需要卸载。

01

「TSForge激活原理示意图」

可以简单的理解为：本身电话激活的CID需要验证真伪，然后SPP验证通过后放行，SPP API将其存储即可激活。采用TSForge方案，CID虽然是假的，但是有了RSA密钥可以直接让SPP API存储假数据。画了两张图帮助大家理解![图片](https://res.wx.qq.com/t/wx_fed/we-emoji/res/v1.3.10/assets/newemoji/Yellowdog.png)

![图片](https://mmbiz.qpic.cn/mmbiz_png/pxopGibm8O53aUTs787AQ3KS6nxIYTDmg33GNwyicd6yDNgUvWD1ib7dFUAsEWXXtWXLemoWIPJUhLwGUynDSER5Q/640?wx_fmt=png&from=appmsg)

正常电话激活

![图片](https://mmbiz.qpic.cn/mmbiz_png/pxopGibm8O53aUTs787AQ3KS6nxIYTDmgLiaicYf1gPica8fdt0a4t6zgYGiapjE9ckUdIPQic82yviatB5xLejWou3WQ/640?wx_fmt=png&from=appmsg)

TSForge

02

「TSForge支持哪些版本？」

通过上述原理可以发现，只要是支持微软电话激活且是SPP接管的Windows和Office版本，均可以采用TSForge离线永久激活。

Windows Vista到Windows 11之间的所有系统（包括Windows Server）均支持，但是Vista和Server 2008通过权限保护，防止用户篡改数据，因此只能在PE下才可以实现。所以，号称Windows 7~Windows 11包括Server全都支持！  

Windows 8及以上系统中，安装的Office 2013~2024都是SPP接管的，所以均支持。

以下版本不支持：

①Office 2010是OSPP接管的，因此它不适用。

②Windows 7上安装的Office 2013也是被OSPP接管的，所以也不适用。

③Office 365由于不支持电话激活，所以也不适用。

03

「TSForge支持Win10数字激活吗？」

TSForge激活后，即便联网也不是数字激活，重装系统后还需要重新激活。TSForge最大的好处是，以前那些不容易永久激活的版本现在变得简单了，比如Windows 7 ESU以及KMS主机（使用CSVLK密钥激活）等。再比如Windows Server、Windows 7以前使用OEM激活，存在一定的蓝屏或无法进入系统的概率，那么现在不用担心这个问题了。

另外，通过TSForge还可以实现KMS激活4000年...

04

「MAS 3.0下载」

①MAS 3.0版已经集成了TSForge激活功能，可以下载使用。下载地址：

https://heu168.lanzouo.com/b00p2g1pa 密码:ehio

![图片](https://mmbiz.qpic.cn/mmbiz_png/pxopGibm8O50RAb82gQVuWUcdJfib5ouWxDWLGnLf4CuUOhLX7zgOrlfaZT8SI7y4gmPxpNz94lFCyJziaC0Fqu8Q/640?wx_fmt=png&from=appmsg)

②MAS还可以在线使用，打开PowerShell 输入：

> irm https://get.activated.win | iex

其实就是在线将MAS下载到本地后运行，好处是可以下载到最新版，不用存储太多版本；坏处是，网速不好下载困难。  

![图片](https://mmbiz.qpic.cn/mmbiz_gif/pxopGibm8O52d8eINIGkm9rTul40KrkZ18vxpp0hHLYmqsrbkAMxTjkMTag390neVvZl52KgibibZySz1ee3JgfJA/640?wx_fmt=gif)