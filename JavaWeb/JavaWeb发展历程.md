<https://zhuanlan.zhihu.com/p/94983500>

## 源起
在上世纪90年代，因为nternet和浏览器的飞速发展，使得基于浏览器的B/S模式随之火爆发展起来。最初，用户使用浏览器向WEB服务器发送的请求都是请求静态的资源，比如html、css等。 但是可以想象：根据用户请求的不同动态的处理并返回资源是理所当然必须的要求，例如用户提交一些东西，服务器就能按提交的内容反馈用户不同的效果。所以人们应该非常迫切想要推出一项技术来实现动态的处理， java 为了应对上述需求，促进了servlet技术诞生。

## Servlet诞生
SUN公司刚刚推出JavaEE（Java企业版）时，推出了Servlet这个东西，命名就是Service+Applet，即服务小程序。Servlet可以说是Java技术中最早的Web解决方案，Servlet与普通Java类的编写非常类似。在Servlet中可以通过挨着行输出Html等语句来实现页面的样式和输出，数据的动态功能当然也就实现了。表现、逻辑、控制、业务全部混在Servlet类中。下面给出一个简单例子来直观感受一下。
```
public void doGet(HttpServletRequest request,HttpServletResponse) throws IOException,ServletException
{
    response.setContentType("text/html;charset=gb2312");
    PrintWriter out = response.getWriter();
    out.println("<html>");
    out.println("<head><title>Hello World！</title></head>");
    out.println("<body>");
    out.println("<p>Hello World！</p>");
    out.println("</body></html>");
}
```
这样就动态的生成了一个内容为Hello World!的HTML页面在浏览器上显示。

开发servlet必须精通网页前端和美工，你得非常不直观的在Servlet中写前端代码，这使得实现各种页面效果和风格非常困难。对于后端来说，所有的业务逻辑、页面跳转、样式表现全部混杂在同一个类中，并且一项业务一般只有一个Servlet类与其对应，实在是…太麻烦了。所以为了解决这些问题，sun公司借鉴 微软的asp,正式推出了JSP（servlet1.1）；

## JSP诞生
经过纯Servlet开发的噩梦之后，Sun公司又推出（或者说是倡导）了JSP技术，全称是Java Server Page，JSP中采用HTML语言直接生成界面，还可以在界面中使用<% %>脚本标识嵌入Java代码，揪其本质也是最终生成一个Servlet类来编译解析。如果要开发具有大量网页内容的网站，可以先使用网页编辑工具编写网页，然后在网页中嵌入处理代码即可。再来一个简单的例子：
```
<html>
    <head><title>测试</title></head>
    <body>
        显示的内容是：<% String aa= “hello” ; out.println(aa); %>
    </body>
</html>
```
虽然JSP可以实现网站的快速开发，但依然存在缺点：网站的输入输出、处理、控制全部夹杂在一起，维护不方便，即使只需要修改该页面的一个简单按钮文本，或者一段静态的文本内容，也不得不打开混杂的动态脚本的页面源文件进行修改。当网站中需要进行大量的处理代码的时候，JSP文件将很难维护，并且代码也不容易共享。前端开发人员需要看大量他看不懂的后端代码；同样，servlet开发人员也在复杂的前端代码中找到其能写servlet代码的地方;

因为JSP在编写网页方面具有优势，而编写处理代码存在很多问题，所以人们把JSP中的处理代码使用JavaBean来实现。于是出现了JSP+JavaBean的开发模式（也叫做Model1开发模式）。

## JSP+JavaBean
这里就要首先弄清楚JavaBean到底是啥?JavaBean 是一种JAVA语言写成的可重用组件。为写成JavaBean，类必须是具体的和公共的，并且具有无参数的构造器。JavaBean 通过提供符合一致性设计模式的公共方法将内部域暴露成员属性，set和get方法获取。其实可以理解就是Java类，这里我理解为JavaBean的出现作为和数据库交互的类，Jsp页面里边中写部分Java代码用于转发等操作以及HTML页面的生成代码，而获取数据的方式以及部分业务逻辑则通过JavaBean来实现。

这种开发模式有一个简单的分层——表现层和数据业务层，但是在表现层中视图和控制逻辑依然混淆在一起，后期维护依旧困难，因此，原有的Model1开发模式慢慢转变成Model2开发模式，即Servlet+JSP+JavaBean。同时也使得在Web项目中将MVC设计模式实现。

## Servlet+JSP+JavaBean
在这种开发模式下，JSP页面中就可以不用任何的<%%>语句了，包括<%=%>全部用EL表达式来代替，列表的遍历和条件判断等（Java中的for循环和if语句）也可以通过JSTL来代替。 这样的话视图层相比较之前的开发模式来说要薄得多的多，JSP中不涉及任何的业务逻辑，前端人员修改样式也十分方便。

控制逻辑通过Servlet来实现，获取前台传的参数、控制页面跳转，封装对象、向前台传输对象或者参数。并且可以由自己设计，设法用一个Servlet类实现该模块的所有功能的页面跳转。

而JavaBean再一次进行分割为：业务逻辑、数据持久化。

## 三层架构与MVC设计模式
这里首先讨论一下三层架构和MVC的关系。我们大多数人总会把三层架构和MVC混为一谈，其实并非如此。三层架构是指软件系统的整体设计分层：业务逻辑层、数据持久化层和表现层。而MVC设计模式只体现在表现层中，即将表现层又分为模型、视图和控制器。所以在上面Model2模式下对JavaBean分割后形成了三层架构与MVC的全新的JavaWeb开发模式。

## EJB


## Spring

