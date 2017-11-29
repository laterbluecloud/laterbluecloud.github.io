---
layout: post
title:  "测试网页嵌入网易音乐"
date:   2017-11-28 16:00:00
categories: personal_test
excerpt: music_test
---

* content
{:toc}


### 尝试一下插入网易云音乐
  

<html>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="330" height="86" src="https://music.163.com/outchain/player?type=2&id=170749&auto=1&height=66"></iframe>
</html>

其中遇到了一些问题,总结如下:
网易给的代码如下:

{% highlight html linenos %}
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 
src="//music.163.com/outchain/player?type=2&id=170749&auto=1&height=66"></iframe>
{% endhighlight %}
其中关键是2个问题
1  网址前面需要加 "https:"
2  width=300 height = 86 其中的参数需要加引号

正确的代码如下:
{% highlight html linenos %}
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="330" height="86" 
src="https://music.163.com/outchain/player?type=2&id=170749&auto=1&height=66"></iframe>

{% endhighlight %}


		



