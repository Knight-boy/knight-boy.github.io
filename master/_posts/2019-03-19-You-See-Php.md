---
title: "Php"
date: 2019-03-19
image: /assets/img/blog/php-logo.svg
description: >
  php语言精粹
author: author2
comments: true
---

对于web开发而言，php是一种必不可少的脚本语言，可以通过它在客户端与服务器间进行数据交换。虽然现在各种框架层出不穷，但绝大多数的公司仍然以[Php](https://www.php.net/manual/en/)、[Ｍysql](https://dev.mysql.com/doc/)、[Nginx](https://nginx.org/en/)、[Apache](https://www.apache.org/)等搭配作为服务器器前后端的基本架构。

#### 文本信息的读写

举例：

```php+HTML
//handle "\n"
<?php
  $document_root = $_SERVER['DOCUMENT_ROOT'];
?>
<!DOCTYPE html>
<html>
  <head>
    <title>one line txt</title>
  </head>
  <body>
    <?php
    	@$fp = fopen('$document_root/../girl.txt');
    	flock($fp,LOCK_SH);
    if(!$fp) {
      echo "<p><strong>No data pending.<br/>
      	Please try again later.</strong></p>";
      exit;
    }
    while(!feof($fp)) {
      $char = fgetc($fp);
      if(!feof($fp)) {
        echo ($char='\n' ? "" : $char);
      }
    }
    flock($fp,LOCK_UN);
    fclose($fp);
    ?>
  </body>
</html>
```



#### MySql的读和写

#### 资源：

[php.net](http://php.net/manual/zh/)

[Php和MySQL Web开发](http://www.xz577.com/e/180.html)(**请支持正版**)