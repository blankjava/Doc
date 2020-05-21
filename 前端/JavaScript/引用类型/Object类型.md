## 创建对象
到目前为止，我们看到的大多数引用类型值都是Object类型的实例；而且，Object也是ECMAScript中使用最多的一个类型。虽然Object的实例不具备多少功能，但对于在应用程序中存储和传输数据而言，它们确实是非常理想的选择。

创建Object实例的方式有两种。第一种是使用new操作符后跟Object构造函数，如下所示：
```
var person = new Object();
person.name = 'Nicholas';
person.age = 29;
```
另一种方式是使用**对象字面量**表示法。对象字面量是对象定义的一种简写形式，目的在于简化创建包含大量属性的对象的过程。下面这个例子就使用了对象字面量语法定义了与前面那个例子中相同的person对象：
```
var person = {
    name : 'Nicholas',
    age : 29
};
```
我们定义了name属性，之后是一个冒号，再后面是这个属性的值。在对象字面量中，使用逗号来分隔不同的属性，因此'Nicholas'后面是一个逗号。但是，在age属性的值29的后面不能添加逗号，因为age是这个对象的最后一个属性。在最后一个属性后面添加逗号，会在IE7及更早版本和Opera中导致错误。

在使用对象字面量语法时，属性名也可以使用字符串，如下面这个例子所示。
```
var person = {
    'name' : 'Nicholas',
    'age' : 29,
    5 : true
};
```
这个例子会创建一个对象，包含三个属性：name、age和5。但这里的数值属性名会自动转换为字符串。

另外，使用对象字面量语法时，如果留空其花括号，则可以定义只包含默认属性和方法的对象，如下所示：
```
var person = {};         //与new Object()相同
person.name = 'Nicholas';
person.age = 29;
```

虽然可以使用前面介绍的任何一种方法来定义对象，但开发人员更青睐对象字面量语法，因为这种语法要求的代码量少，而且能够给人封装数据的感觉。实际上，对象字面量也是向函数传递大量可选参数的首选方式，例如：
```
function displayInfo(args) {
    var output = '';

    if (typeof args.name == 'string'){
        output += 'Name: ' + args.name + '\n';
    }

    if (typeof args.age == 'number') {
        output += 'Age: ' + args.age + '\n';
    }

    alert(output);
}

displayInfo({ 
    name: 'Nicholas', 
    age: 29
});

displayInfo({
    name: 'Greg'
});
```
在这个例子中，函数displayInfo()接受一个名为args的参数。这个参数可能带有一个名为name或age的属性，也可能这两个属性都有或者都没有。在这个函数内部，我们通过typeof操作符来检测每个属性是否存在，然后再基于相应的属性来构建一条要显示的消息。然后，我们调用了两次这个函数，每次都使用一个对象字面量来指定不同的数据。这两次调用传递的参数虽然不同，但函数都能正常执行。

## 访问属性
一般来说，访问对象属性时使用的都是点表示法，这也是很多面向对象语言中通用的语法。不过，在JavaScript也可以使用方括号表示法来访问对象的属性。在使用方括号语法时，应该将要访问的属性以字符串的形式放在方括号中，如下面的例子所示。
```
alert(person['name']);          //'Nicholas'
alert(person.name);             //'Nicholas'
```
从功能上看，这两种访问对象属性的方法没有任何区别。但方括号语法的主要优点是可以通过变量来访问属性，例如：
```
var propertyName = 'name';
alert(person[propertyName]);    //'Nicholas'
```
如果属性名中包含会导致语法错误的字符，或者属性名使用的是关键字或保留字，也可以使用方括号表示法。例如：
```
person['first name'] = 'Nicholas';
```
由于'first name'中包含一个空格，所以不能使用点表示法来访问它。然而，属性名中是可以包含非字母非数字的，这时候就可以使用方括号表示法来访问它们。

通常，除非必须使用变量来访问属性，否则我们建议使用点表示法。