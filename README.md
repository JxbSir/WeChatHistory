# iOS/OSX安全攻防（微信篇）

`My笔记` `OSX开发` `OSX Develop` `runtime` `dylib`


在写这个工程项目之前，我看过一篇文章《iOS冰与火之歌番外篇》，当时这篇讲述了不需要进行越狱就能写一个微信的抢红包插件，瞬间感觉还是很牛逼的，但是此文章只讲述了部分信息，还有3章的信息没有公开出来，一般想要抢红包插件的人也是一头雾水，不知道如何下手，我也没有例外。

过了一段时间之后，突然来了灵感，runtime这个东西，是可以无所不能的，发挥自己的想法，我自己也可以写一个。不过我并没有去写抢红包插件，因为我目前的公司主要的聊天工具是微信，但是大家都知道，Mac版的微信是没有聊天记录功能的，第二天，昨天的聊天记录不见了，想要记录，只能去手机上翻翻，是不是很抓狂，所以我决定为Mac的微信添加一个功能【历史聊天记录】。


#技术原理
使用`dylib动态库`注入到App，再使用`runtime黑魔法`替换原生函数重新实现新的逻辑。

###创建dylib
- Installation Directory 设置成 @executable_path
- Mach-O Type 设置成 Dynamic Library
- Executable Prefix 设置成 空
```objc
//注入到app后，启动app会自动执行此entry函数，然后就可以发挥你的想象
__attribute__((constructor)) static void entry()
{
    NSLog(@"Load test dylib Success");
}
```

###注入App
yololib源代码：https://github.com/KJCracks/yololib
```bash
./yololib WeChat /usr/local/lib/libWechatHistory.dylib
```
注入后需要将dylib拷贝到与二进制文件同目录下面

###签名
```objc
codesign -f -s "Developer ID Application: Xuebin Jin (XXXXXXXX)" WeChat.app
```
若打开被拒绝请执行：
```bash
xattr -d -r com.apple.quarantine /Applications/WeChat.app
```

###截图
![图片1](https://raw.githubusercontent.com/JxbSir/WeChatHistory/master/1.jpg)
![图片2](https://raw.githubusercontent.com/JxbSir/WeChatHistory/master/2.jpg)


##总结
其实原理很简单，了解后人人都可以写，`源码稍后`会开源出来。
