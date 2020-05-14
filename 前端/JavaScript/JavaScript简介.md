通常我们说的JavaScript其实由三部分组成：ECMAScript、BOM以及DOM；
- 核心——ECMAScript：提供核心语言功能
- 浏览器对象模型——BOM：提供与浏览器交互的方法和接口
- 文档对象模型——DOM：提供访问和操作网页内容的方法和接口

JavaScript是弱类型，基于对象的脚本语言（基于对象的语言还有VB）。而Java是面向对象的语言，所谓的基于对象是指对象是即时创建的，可以动态修改，没有类的概念，而面向对象是指对象本身需要我们自己定义类（模板）后再实现化得到。

## ECMAScript
ECMAScript是JavaScript的核心，但它并不依赖JS或浏览器而存在。JS实现了ECMAScript，Adobe Flash也实现了ECMAScript。浏览器是ECMAScript的众多宿主环境之一，Node，Flash都是他的宿主环境。宿主环境不仅实现了ECMAScript，还提供了相应功能的拓展。比如JS就提供了DOM。

## DOM
最初的DOM1级由两个部分组成：DOM Core和DOM HTML。DOM Core规定了如何映射基于XML的文档结构，以便简化对文档中任意部分的访问操作。DOM HTML则增加了针对HTML的对象和方法。HTML是基于XML的标记语言。与XML不同的是，HTML的标签都是固定的，且同时担任着显示数据的任务。XML的数据标签是不定的，且不负责显示。还有些其他的语言也基于标记语言：SVG，MathML等。 

DOM2添加了一些新的模块：
- DOM Views：定义了跟踪不同文档视图的接口
- DOM Events：定义了事件和处理事件的接口（现在JS最重要的特点就基于此）
- DOM Style：基于CSS为元素应用样式的接口
- DOM Traversal and Range：定义了便利和操作文档树的接口 

DOM3添加了新的模块：
- DOM Load and Save：统一的加载和保存文档的方式
- DOM Validation：验证文档 
- DOM3还对DOM Core做了拓展，支持XML 1.0

## BOM
BOM只处理与浏览器有关的部分，包括一些扩展：弹出新窗口，窗口分辨率的等，JS中常用的window对象就是BOM的一部分。
