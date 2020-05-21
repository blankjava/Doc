ECMAScript中的Date类型是在早期Java中的java.util.Date类基础上构建的。为此，Date类型使用自UTC（Coordinated Universal Time，国际协调时间）1970年1月1日午夜（零时）开始经过的毫秒数来保存日期。在使用这种数据存储格式的条件下，Date类型保存的日期能够精确到1970年1月1日之前或之后的285 616年。

要创建一个日期对象，使用new操作符和Date构造函数即可，如下所示。
```
var now = new Date();
```
