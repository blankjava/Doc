## 创建Date
ECMAScript中的Date类型是在早期Java中的java.util.Date类基础上构建的。为此，Date类型使用自UTC（Coordinated Universal Time，国际协调时间）1970年1月1日午夜（零时）开始经过的毫秒数来保存日期。在使用这种数据存储格式的条件下，Date类型保存的日期能够精确到1970年1月1日之前或之后的285 616年。

要创建一个日期对象，使用new操作符和Date构造函数即可，如下所示。
```
var now = new Date();
```
在调用Date构造函数而不传递参数的情况下，新创建的对象自动获得当前日期和时间。如果想根据特定的日期和时间创建日期对象，必须传入表示该日期的毫秒数（即从UTC时间1970年1月1日午夜起至该日期止经过的毫秒数）。为了简化这一计算过程，ECMAScript提供了两个方法：Date.parse()和Date.UTC()。

其中，Date.parse()方法接收一个表示日期的字符串参数，然后尝试根据这个字符串返回相应日期的毫秒数。ECMA-262没有定义Date.parse()应该支持哪种日期格式，因此这个方法的行为因实现而异，而且通常是因地区而异。将地区设置为美国的浏览器通常都接受下列日期格式：
- “月/日/年”，如6/13/2004；
- “英文月名 日,年”，如January 12,2004；
- “英文星期几 英文月名 日 年 时:分:秒 时区”，如Tue May 25 2004 00:00:00 GMT-0700。
- ISO 8601扩展格式YYYY-MM-DDTHH:mm:ss.sssZ（例如2004-05-25T00:00:00）。只有兼容ECMAScript 5的实现支持这种格式。

例如，要为2004年5月25日创建一个日期对象，可以使用下面的代码：
```
var someDate = new Date(Date.parse('May 25, 2004'));
```
如果传入Date.parse()方法的字符串不能表示日期，那么它会返回NaN。实际上，如果直接将表示日期的字符串传递给Date构造函数，也会在后台调用Date.parse()。换句话说，下面的代码与前面的例子是等价的：
```
var someDate = new Date('May 25, 2004');
```
这行代码将会得到与前面相同的日期对象。

Date.UTC()方法同样也返回表示日期的毫秒数，但它与Date.parse()在构建值时使用不同的信息。Date.UTC()的参数分别是年份、基于0的月份（一月是0，二月是1，以此类推）、月中的哪一天（1到31）、小时数（0到23）、分钟、秒以及毫秒数。在这些参数中，只有前两个参数（年和月）是必需的。如果没有提供月中的天数，则假设天数为1；如果省略其他参数，则统统假设为0。以下是两个使用Date.UTC()方法的例子：
```
// GMT时间2000年1月1日午夜零时
var y2k = new Date(Date.UTC(2000, 0));

// GMT时间2005年5月5日下午5:55:55
var allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55));
```
这个例子创建了两个日期对象。第一个对象表示GMT时间2000年1月1日午夜零时，传入的值一个是表示年份的2000，一个是表示月份的0（即一月份）。因为其他参数是自动填充的（即月中的天数为1，其他所有参数均为0），所以结果就是该月第一天的午夜零时。第二个对象表示GMT时间2005年5月5日下午5:55:55，即使日期和时间中只包含5，也需要传入不一样的参数：月份必须是4（因为月份是基于0的）、小时必须设置为17（因为小时以0到23表示），剩下的参数就很直观了。

如同模仿Date.parse()一样，Date构造函数也会模仿Date.UTC()，但有一点明显不同：日期和时间都基于本地时区而非GMT来创建。不过，Date构造函数接收的参数仍然与Date.UTC()相同。因此，如果第一个参数是数值，Date构造函数就会假设该值是日期中的年份，而第二个参数是月份，以此类推。据此，可以将前面的例子重写如下。
```
// 本地时间2000年1月1日午夜零时
var y2k = new Date(2000, 0);

// 本地时间2005年5月5日下午5:55:55
var allFives = new Date(2005, 4, 5, 17, 55, 55);
```
以上代码创建了与前面例子中相同的两个日期对象，只不过这次的日期都是基于系统设置的本地时区创建的。

## 当前日期和时间
ECMAScript 5添加了Date.now()方法，返回表示调用这个方法时的日期和时间的毫秒数。这个方法简化了使用Date对象分析代码的工作。例如：
```
//取得开始时间
var start = Date.now();

//调用函数
doSomething();

//取得停止时间
var stop = Date.now(),
result = stop – start;
```
支持Data.now()方法的浏览器包括IE9+、Firefox 3+、Safari 3+、Opera 10.5和Chrome。在不支持它的浏览器中，使用+操作符把Data对象转换成字符串，也可以达到同样的目的。
```
//取得开始时间
var start = +new Date();

//调用函数
doSomething();
//取得停止时间
var stop = +new Date(),
result = stop – start;
```

## 继承的方法
与其他引用类型一样，Date类型也重写了toLocaleString()、toString()和valueOf()方法；但这些方法返回的值与其他类型中的方法不同。Date类型的toLocaleString()方法会按照与浏览器设置的地区相适应的格式返回日期和时间。这大致意味着时间格式中会包含AM或PM，但不会包含时区信息（当然，具体的格式会因浏览器而异）。而toString()方法则通常返回带有时区信息的日期和时间，其中时间一般以军用时间（即小时的范围是0到23）表示。

但是，这两个方法在不同的浏览器中返回的日期和时间格式可谓大相径庭。事实上， toLocaleString()和toString()的这一差别仅在调试代码时比较有用，而在显示日期和时间时没有什么价值。

至于Date类型的valueOf()方法，则根本不返回字符串，而是返回日期的毫秒表示。因此，可以方便使用比较操作符（小于或大于）来比较日期值。请看下面的例子。
```
var date1 = new Date(2007, 0, 1);                    //'January 1, 2007'
var date2 = new Date(2007, 1, 1);                    //'February 1, 2007'

alert(date1 < date2); //true
alert(date1 > date2); //false
```
从逻辑上讲，2007年1月1日要早于2007年2月1日，此时如果我们说前者小于后者比较符合常理。而表示2007年1月1日的毫秒值小于表示2007年2月1日的毫秒值，因此在首先使用小于操作符比较日期时，返回的结果是true。这样，就为我们比较日期提供了极大方便。

## 日期格式化方法
Date类型还有一些专门用于将日期格式化为字符串的方法，这些方法如下。
- toDateString()——以特定于实现的格式显示星期几、月、日和年；
- toTimeString()——以特定于实现的格式显示时、分、秒和时区；
- toLocaleDateString()——以特定于地区的格式显示星期几、月、日和年；
- toLocaleTimeString()——以特定于实现的格式显示时、分、秒；
- toUTCString()——以特定于实现的格式完整的UTC日期。

与toLocaleString()和toString()方法一样，以上这些字符串格式方法的输出也是因浏览器而异的，因此没有哪一个方法能够用来在用户界面中显示一致的日期信息。

除了前面介绍的方法之外，还有一个名叫toGMTString()的方法，这是一个与toUTCString()等价的方法，其存在目的在于确保向后兼容。不过，ECMAScript推荐现在编写的代码一律使用toUTCString()方法。
```
alert(new Date().toUTCString()); //Fri, 22 May 2020 01:31:42 GMT
```
这些日期时间格式化方法都不灵活，在实际项目开发中，还是需要获取到日期时间的各部分数值进行拼接成需要的日期时间格式；

## 日期/时间组件方法
到目前为止，剩下还未介绍的Date类型的方法（如下表所示），都是直接取得和设置日期值中特定部分的方法了。需要注意的是，UTC日期指的是在没有时区偏差的情况下（将日期转换为GMT时间）的日期值。
|方　　法|	说　　明|
|:-|:-|
|getTime()|	返回表示日期的毫秒数；与valueOf()方法返回的值相同|
|setTime(毫秒)|	以毫秒数设置日期，会改变整个日期|
|getFullYear()	|取得4位数的年份（如2007而非仅07）|
|getUTCFullYear()|	返回UTC日期的4位数年份|
|setFullYear(年)	|设置日期的年份。传入的年份值必须是4位数字（如2007而非仅07）|
|setUTCFullYear(年)|	设置UTC日期的年份。传入的年份值必须是4位数字（如2007而非仅07）|
|getMonth()	|返回日期中的月份，其中0表示一月，11表示十二月|
|getUTCMonth()|	返回UTC日期中的月份，其中0表示一月，11表示十二月|
|setMonth(月)|	设置日期的月份。传入的月份值必须大于0，超过11则增加年份|
|setUTCMonth(月)|	设置UTC日期的月份。传入的月份值必须大于0，超过11则增加年份|
|getDate()	|返回日期月份中的天数（1到31）|
|getUTCDate()|	返回UTC日期月份中的天数（1到31）|
|setDate(日)	|设置日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份|
|setUTCDate(日)|	设置UTC日期月份中的天数。如果传入的值超过了该月中应有的天数，则增加月份|
|getDay()	|返回日期中星期的星期几（其中0表示星期日，6表示星期六）|
|getUTCDay()	|返回UTC日期中星期的星期几（其中0表示星期日，6表示星期六）|
|getHours()	|返回日期中的小时数（0到23）|
|getUTCHours()|	返回UTC日期中的小时数（0到23）|
|setHours(时)|	设置日期中的小时数。传入的值超过了23则增加月份中的天数|
|setUTCHours(时)|	设置UTC日期中的小时数。传入的值超过了23则增加月份中的天数|
|getMinutes()	|返回日期中的分钟数（0到59）|
|getUTCMinutes()	|返回UTC日期中的分钟数（0到59）|
|setMinutes(分)	|设置日期中的分钟数。传入的值超过59则增加小时数|
|setUTCMinutes(分)|	设置UTC日期中的分钟数。传入的值超过59则增加小时数|
|getSeconds()	|返回日期中的秒数（0到59）|
|getUTCSeconds()	|返回UTC日期中的秒数（0到59）|
|setSeconds(秒)	|设置日期中的秒数。传入的值超过了59会增加分钟数|
|setUTCSeconds(秒)|	设置UTC日期中的秒数。传入的值超过了59会增加分钟数|
|getMilliseconds()|	返回日期中的毫秒数|
|getUTCMilliseconds()|	返回UTC日期中的毫秒数|
|setMilliseconds(毫秒)|	设置日期中的毫秒数|
|setUTCMilliseconds(毫秒)|	设置UTC日期中的毫秒数|
|getTimezoneOffset()	|返回本地时间与UTC时间相差的分钟数。例如，美国东部标准时间返回300。在某地进入夏令时的情况下，这个值会有所变化|

将日期时间转换为指定格式，如：YYYY-mm-dd HH:MM表示:
```
function dateFormat(fmt, date) {
    let ret;
    const opt = {
        "Y+": date.getFullYear().toString(),        // 年
        "m+": (date.getMonth() + 1).toString(),     // 月
        "d+": date.getDate().toString(),            // 日
        "H+": date.getHours().toString(),           // 时
        "M+": date.getMinutes().toString(),         // 分
        "S+": date.getSeconds().toString()          // 秒
        // 有其他格式化字符需求可以继续添加，必须转化成字符串
    };
    for (let k in opt) {
        ret = new RegExp("(" + k + ")").exec(fmt);
        if (ret) {
            fmt = fmt.replace(ret[1], (ret[1].length == 1) ? (opt[k]) : (opt[k].padStart(ret[1].length, "0")))
        };
    };
    return fmt;
}

let date = new Date()
dateFormat("YYYY-mm-dd HH:MM", date)
```

