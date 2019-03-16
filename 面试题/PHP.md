## 请写出几组PHP数组字符串操作函数，并说明用途

- array_column 返回数组中指定的一列
- array_merge  合并两个数组
- in_array     判断数组中是否有某个值
- array_key_exist 判断数组中某个key是否存在
- str_pos      查找字符串首次出现的位置
- substr       字符串截取
- trim         去除字符串两边的空格


## include 和 require 的区别，include_once 和 require_once的区别

最大的区别就是：include在引入不存文件时产生一个警告且脚本还会继续执行，而require则会导致一个致命性错误且脚本停止执行。

include()与require()的功能相同，但在用法上却有一些不同，include()是有条件包含函数，而 require()则是无条件包含函数。

include_once （require_once）语句在脚本执行期间包含并运行指定文件。此行为和 include （require）语句类似，区别是如果该文件中的代码已经被包含了，则不会再次包含，只会包含一次。include_once（require_once）需要查询一遍已加载的文件列表, 确认是否存在, 然后再加载。

## PHP 中, Interface 和 abstract Class 的区别和他们的应用场景？

### 相同点
两者都是抽象类，都不能实例化。

interface实现类及abstrct class的子类都必须要实现已经声明的抽象方法。

### 不同点
interface需要实现，要用implements，而abstract class需要继承，要用extends。

一个类可以实现多个interface，但一个类只能继承一个abstract class。

尽管interface实现类及abstrct class的子类都必须要实现相应的抽象方法，但实现的形式不同。interface中的每一个方法都是抽象方法，都只是声明的(declaration, 没有方法体)，实现类必须要实现。而abstract class的子类可以有选择地实现


### interface的应用场合
类与类之前需要特定的接口进行协调，而不在乎其如何实现。

作为能够实现特定功能的标识存在，也可以是什么接口方法都没有的纯粹标识。

需要将一组类视为单一的类，而调用者只通过接口来与这组类发生联系。

需要实现特定的多项功能，而这些功能之间可能完全没有任何联系。
### abstract class的应用场合

一句话，在既需要统一的接口，又需要实例变量或缺省的方法的情况下，就可以使用它。最常见的有：

定义了一组接口，但又不想强迫每个实现类都必须实现所有的接口。可以用abstract class定义一组方法体，甚至可以是空方法体，然后由子类选择自己所感兴趣的方法来覆盖。

某些场合下，只靠纯粹的接口不能满足类与类之间的协调，还必需类中表示状态的变量来区别不同的关系。abstract的中介作用可以很好地满足这一点。

规范了一组相互协调的方法，其中一些方法是共同的，与状态无关的，可以共享的，无需子类分别实现；而另一些方法却需要各个子类根据自己特定的状态来实现特定的功能


## PHP 中，面向对象的特征及含义，优点和缺点

### 特征

- 封装

- 继承
- 多态

优点：面向对象编程（OOP）是一种设计范式，同时也是一种程序开发方法。它视对象为程序的基本单元，将程序和数据封装在其中，以提高程序的重用性、灵活性和可扩展性。

缺点：因为封装丢失的运行性能，带来了设计类的额外开销。

## Laravel 的生命周期有哪些？

## PHP 的生命周期有哪些？


## 什么是 CSRF 攻击 ？XSS 攻击？如何防范？

CSRF，跨站请求伪造，攻击方伪装用户身份发送请求从而窃取信息或者破坏系统。

讲述基本原理：用户访问A网站登陆并生成了cookie，再访问B网站，如果A网站存在CSRF漏洞，此时B网站给A网站的请求（此时相当于是用户访问），A网站会认为是用户发的请求，从而B网站就成功伪装了你的身份，因此叫跨站脚本攻击。

CSRF防范：

1.合理规范api请求方式，GET，POST

2.对POST请求加token令牌验证，生成一个随机码并存入session，表单中带上这个随机码，提交的时候服务端进行验证随机码是否相同。

XSS，跨站脚本攻击。

防范：不相信任何输入，过滤输入。


## nginx与php-fpm通信的两种方式

### unix socket
unix socket是一种终端，可以使同一台操作系统上的两个或多个进程进行数据通信。这种方式需要再nginx配置文件中填写php-fpm的pid文件位置，效率要比tcp socket高。

### tcp socket
tcp socket的优点是可以跨服务器，当nginx和php-fpm不在同一台机器上时，只能使用这种方式。


## CGI与fastCGI与PHP-FPM的关系

cgi: 通用网管接口，common gateway interface, 它是Web服务器与外部应用程序（CGI程序）之间传递信息的接口标准，当你动态请求web服务器资源的时候，就需要cgi来处理动态语言和web服务器之间的交流，因为web服务器是没办法直接处理动态请求的。

fastcgi： 是CGI的一个实现

PHP-FPM：是 PHP（Web Application）对 Web Server 提供的 FastCGI 协议的接口程序，