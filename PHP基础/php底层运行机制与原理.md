# PHP 底层运行机制与原理

[https://cloud.tencent.com/developer/article/1055801](https://cloud.tencent.com/developer/article/1055801)

## 设计理念

- 多进程模型： 不同请求间互不干涉
- 弱类型语言：一个变量的类型并不是一开始就确定不变，运行中才会确定并可能发生隐式或显式的类型转换，这种机制的灵活性在web开发中非常方便、高效，具体会在后面PHP变量中详述。
- 引擎+组件模式降低内部耦合
- 中间层（SAPI）隔绝web server和PHP

## 核心架构

![](http://ww2.sinaimg.cn/large/7cc829d3gw1exyap8boh7j20ee0ep0tj.jpg)

- zend 引擎：是PHP的内核部分，它将PHP代码翻译为可执行的opcode并实现相应的处理方法，实现了基本的数据结构内存分配及管理，提供相应的API供外部调用