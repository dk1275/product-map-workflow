

有观众老爷留言:

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHZN365J8r455QENBYtlgXv5XR7MIAr8C35NgFXfofgbx1Uyaa3RhMUcKhSQtU9GspZHK2tEs7b3oI3icwzf9l7QFLKmKfMsib7Hw/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

我今天来就做个测试。

我使用Reasonix来直接的进行结果性对比，最终使用微信开发者工具进行结果的查验。

环境准备：

1、桌面文件夹—测试Token数据：2个文件夹（2份小程序的源码），除了文件夹名称不一致，源码保持一致；

2、Reasonix 桌面端

3、模型：deepseek-v4-flash

分别对应两个文件夹问2个问题：

a问题(调用技能）：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7Rh4AibetOHbC41mKeMz9l0wHGBbicmr2bpThqzPmhvJIuUuyJCfs5LDRvAfD3ofBkzsv5dH3QvIVPFI6icWibCdHTyTE0NCruBYkbaIc9z9b8U/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

b问题（不调用技能）：

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHYeicChqQS5xtRJwTXIWxbyia69bULskXmxEUtomaw8CPWOEzmKm9xpXfjg4VEG7qIjMMfLYHwibwZPVaYBNHaUEsy3QUbbqYSCds/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=2)

从消耗结果来看：

a问题(调用技能）：

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHbEADz0bXW0iaEqTDYDwckco7YubGMBpR7CqLahiaBNLTRmwxfUh6ZjXywpdynREJjEibdVkm8Y8SibFX7biad3kic8l4PfvN9VXoSns/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=3)

b问题（不调用技能）：

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHZlx75hVoxoZjrBWGkwTuD4ibznhxASEyFBOfxgic5BXUVUXxSdJ5kYPVq0CulbNP7efdNGbHrsWoc3yzorIqE6t3dHaQPn1HjOs/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=4)

再看结果：

a问题(调用技能）：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7Rh4AibetOHb7rGpicrDcEtO6icgedBMIgxSjY31mC5tLoDibpIAVfgr0Ugdxibh0sSu7MfbojtWuPQtrActgQYs47UMSiaTSjS6VgxYKvIux7nho/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=5)

测试通过，基础功能可以使用。

b问题（不调用技能）：

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHaQAxicKZqUjg4vltbWLicIpT3TfKNcqCDibyVPVtZJJIA3SdwPjnCuu4LQ2Y6Gxo47sIz2BY5g8pzPIia5Z0CYiaBSPO859VxVtXMY/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=6)

测试通过，基础功能可以使用。

总结：

当前的技能对于有1.0基础上的版本做新的功能的开发作用不是很大。

那我们能不能搞个Bug，测试下呢？

那我直接删除2个对比文件中的index.js中间的第100-202行的代码，再测试一下。

说干就干！

同时发问：

a问题（使用技能）：

```perl
现在微信开发者出现了白屏的问题，请调用product-map-workflow.skill请你解决。
```

b问题（不使用技能）：

```js
现在微信开发者出现了白屏的问题，请你解决。
```

测试结果如下：

a问题（使用技能）： ![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHZu5oicGJzWCuMZlHjoKiatR6TfAVWn1hK3sWliclXHfF5nHDU3K9CXo5rVjD1BkDzAXTVmHHsob5icjVYSwbCGlnAVQK4FqvHA6gI/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=7)

b问题（不使用技能）：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7Rh4AibetOHa820acL8L7GiaIGCgN8QIOVbXpWW7dGWbnjfdm1ZkhawoHTQfjXBNax6Qibhap2jqjCzJ9XqKI8sxAxvOLltiaccdVpV4fya4kG0/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=9)

最终的超级大总结：

1、不调用技能比调用技能多花了4.33倍的钱；

2、不调用技能比调用技能多花了tokens2.43倍

兄弟们，你们自己什么意见？ 评论区留言。

——————————————————————————————

顺便推荐一个我充了钱的AI中转站：

```javascript
https://apikey.fun/register?aff=D88EWGDDEBY9
```

价格如下（仅做推荐）：

![图片](https://mmbiz.qpic.cn/mmbiz_png/7Rh4AibetOHazoBe1ErZWNIOiabITOkFsROXukAKceWxnjGWEPXMcO2spRsysYwkMhWz1wJghUJAsX5zlGsocYlGvWKpGnziakr2SVVibZVaBs4/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=11)

AI学习分享 · 目录

作者提示: 个人观点，仅供参考
