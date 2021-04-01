# 问题汇总：

## 网络问题：

描述：第一次访问任意网站时，会特别特别慢。有了dns缓存之后就很快。

修改dns：sudo resolvectl dns wlp0s20f3 8.8.8.8 4.4.4.4

查看当前dns设置：resolvectl