## script标签
script标签的作用就是引入JS代码；

1、外链式，导入外部JS文件
```
<head>
    <script src="js/index.js"></script>
</head>
```

2、内嵌式，直接写JS代码
```
<head>
    <script>
        function show(){
            alert("hello")
        }
    </script>
</head>
```
3、执行顺序
浏览器会按自上而下的顺序依次解析执行script标签，在一个script中的标签解析完之前是不会解析下一个的，这就令引用的顺序非常重要。一般所有的引用会放在head标签中，但是这会导致在所有script文件加载完之后才会开始显示body标签里的元素。有的做法都是放在body结束之前。

另外script标签还有几个属性可以影响脚本的执行顺序：
- async：立即下载，异步，不影响其他脚本的下载和执行
- defer：立即下载，但脚本延迟到文档被完全解析和显示后再执行，即遇到/html标签。需要注意的是，内部script标签不支持这个属性。在文档中有多个含有defer属性的script标签时，按理说应该是按照出现的先后顺序执行，且都先于DOMContentLoaded事件执行。但是事实并不总是这么理想。使用时要注意。
- src：外部脚本地址
- type：出于种种原因，大家还是使用text/javascript

## noscript标签
这个标签中的元素会在浏览器不支持脚本或脚本被禁用时显示，可用于显示一个提示用的p标签之类的。其他情况下这里的元素被忽略。