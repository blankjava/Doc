style标签的作用就是引入JS代码；

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