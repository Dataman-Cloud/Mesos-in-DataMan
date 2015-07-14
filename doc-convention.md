# 文档风格指南

本文严重参考了leancloud的文档[文案风格指南](http://open.leancloud.cn/copywriting-style-guide.html)

## 风格

 * 一定多检查，确保没有错别字。
 * 即使是流行语中故意的谐音错别字也不要使用，比如「墙裂」、「童鞋」等。
 * 我们崇尚精练的文风。请在检查中把对表达意思没有明显作用的字、词、句删除，在不影响表达效果的前提下把文案长度减到最短。


## 中文、英文、数字混合时空格的使用

 * 英文与非标点的中文之间需要有一个空格，如「使用 OmegaCloud 开发移动应用」而不是「使用OmegaCloud开发移动应用」。1
 * 数字与非标点的中文之间需要有一个空格，如「我们发布了 5 个产品」而不是「我们发布了5个产品」。
 * 尽可能使用中文数词，特别是当前后都是中文时。上面的例子写为「我们发布了五个产品」会更好。
 * 数字与单位之间需要有一个空格，如「5 GB」而不是「5GB」。
 * 注意特殊名词的大小写：Android、iOS、iPhone、Google、Apple，无论是否在句首都应该以同样的方式写。
 * 在官方文案中尽量使用中文，避免中英文混合的情况。例如「app」一般应写为「应用」或「移动应用」。品牌、产品名、人名、地名等特殊名词，如果来自英文，请使用英文以避免在不同译法间选择。

## 标点相关

 * 只有中文或中英文混排中，一律使用中文/全角标点。
 * 中英文混排中如果出现整句英文，则在这句英文中使用英文/半角标点。
 * 中文标点与其他字符间一律不加空格。


## 段落

 * 如果是纯文本，段落之间使用一个空行隔开。如果是 HTML 或其他富文本格式，使用额外空白作为段落间的分隔。
 * 段落开头不要留出空白字符。

## 代码

下面是一段bash代码示例：
  
```bash
  # 分别设置环境变量 ZK1, ZK2 和 ZK3 为3台主机的ip地址
  root@localhost:~$export ZK1=< zookeeper IP1 >
  root@localhost:~$export ZK2=< zookeeper IP2 >
  root@localhost:~$export ZK3=< zookeeper IP3 >
  root@localhost:~$echo "zk://$ZK1:2181,$ZK2:2181,$ZK3:2181/mesos" > /etc/mesos/zk
  root@localhost:~$echo 2 > /etc/mesos-master/quorum
  root@localhost:~$echo `hostname -I` > /etc/mesos-master/ip
  root@localhost:~$echo `hostname -I` > /etc/mesos-master/hostname
  root@localhost:~$echo "DataMan" > /etc/mesos-master/cluster
```

## 对第三方内容的引用

### 如果在正文中部分引用第三方内容，请使用恰当的引用格式并给出出处。如：
> One man’s constant is another man’s variable. — Alan Perlis

### 如果是全篇转载，请在全文开头显著位置注明出处并链接至原文，如：
> 本文转载自 [WikiQuote](http://en.wikiquote.org/wiki/Alan_Perlis)

### 如果格式不允许超链接，请以文本方式直接给出原文链接。如果原文链接太长影响美观，可以使用短链接服务。如
> 本文转载自 WikiQuote：http://bit.ly/UlHIdN

## 细节问题

 * 「你」和「您」：在不是很正式或没有明确的个体指代对象的时候请用「你」，如文档、博客、群发的邮件等；在指代特定个体时请用「您」，如活动邀请函等。
 * 字体和字号的一致：在富文本格式文档中，特别是 HTML 邮件中，常有人因为从不同来源复制粘贴而导致同一层次的文本字体和字号不一致。这给人不专业的感觉，请避免。
 * App 是 application 的缩写，发音为 /ˈæp/，所以要注意不要把三个字母拆开念。App 是一个普通名词而不是多个单词的首字母缩写，所以不应该用全大写的 APP。和其他词一样，大小写规则取决于是否处于句首、标题、或特殊短语（如 App Store）中。大多数情况下应该使用中文「应用」以避免这样的问题。


## 推荐阅读

 * [余光中：怎样改进英式中文？——论中文的常态与变态](http://open.leancloud.cn/improve-chinese.html)
 * 「The Elements of Style」，虽然是讲英语，但很多对中文也适用。
 * 有研究顯示，打字的時候不喜歡在中文和英文之間加空格的人，感情路都走得很辛苦，有七成的比例會在 34 歲的時候跟自己不愛的人結婚，而其餘三成的人最後只能把遺產留給自己的貓。畢竟愛情跟書寫都需要適時地留白。 — [vinta/paranoid-auto-spacing](https://github.com/vinta/paranoid-auto-spacing)
