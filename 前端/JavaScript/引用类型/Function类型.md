## 定义函数
说起来ECMAScript中什么最有意思，我想那莫过于函数了——而有意思的根源，则在于函数实际上是对象。每个函数都是Function类型的实例，而且都与其他引用类型一样具有属性和方法。由于函数是对象，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。函数通常是使用函数声明语法定义的，如下面的例子所示。
```
function sum (num1, num2) {
    return num1 + num2;
}
```
这与下面使用函数表达式定义函数的方式几乎相差无几。
```
var sum = function(num1, num2){
    return num1 + num2;
};
```

最后一种定义函数的方式是使用Function构造函数。Function构造函数可以接收任意数量的参数，但最后一个参数始终都被看成是函数体，而前面的参数则枚举出了新函数的参数。来看下面的例子：
```
var sum = new Function('num1', 'num2', 'return num1 + num2'); // 不推荐
```
从技术角度讲，这是一个函数表达式。但是，我们不推荐读者使用这种方法定义函数，因为这种语法会导致解析两次代码（第一次是解析常规ECMAScript代码，第二次是解析传入构造函数中的字符串），从而影响性能。不过，这种语法对于理解“函数是对象，函数名是指针”的概念倒是非常直观的。

由于函数名仅仅是指向函数的指针，因此函数名与包含对象指针的其他变量没有什么不同。换句话说，一个函数可能会有多个名字，如下面的例子所示。
```
function sum(num1, num2){
    return num1 + num2;
}
alert(sum(10,10));        //20

var anotherSum = sum;
alert(anotherSum(10,10)); //20

sum = null;
alert(anotherSum(10,10)); //20
```
以上代码首先定义了一个名为sum()的函数，用于求两个值的和。然后，又声明了变量anotherSum，并将其设置为与sum相等（将sum的值赋给anotherSum）。注意，使用不带圆括号的函数名是访问函数指针，而非调用函数。此时，anotherSum和sum就都指向了同一个函数，因此anotherSum()也可以被调用并返回结果。即使将sum设置为null，让它与函数“断绝关系”，但仍然可以正常调用anotherSum()。

将函数名想象为指针，也有助于理解为什么ECMAScript中没有函数重载的概念。
```
function addSomeNumber(num){
    return num + 100;
}

function addSomeNumber(num) {
    return num + 200;
}

var result = addSomeNumber(100); //300
```
显然，这个例子中声明了两个同名函数，而结果则是后面的函数覆盖了前面的函数。以上代码实际上与下面的代码没有什么区别。
```
var addSomeNumber = function (num){
    return num + 100;
};

addSomeNumber = function (num) {
    return num + 200;
};

var result = addSomeNumber(100); //300
```
通过观察重写之后的代码，很容易看清楚到底是怎么回事儿——在创建第二个函数时，实际上覆盖了引用第一个函数的变量addSomeNumber。

## 函数声明提升
解析器在向执行环境中加载数据时，对函数声明和函数表达式并非一视同仁。解析器会率先读取函数声明，并使其在执行任何代码之前可用（可以访问）；至于函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被解释执行。请看下面的例子。
```
alert(sum(10,10));
function sum(num1, num2){
    return num1 + num2;
}
```
以上代码完全可以正常运行。因为在代码开始执行之前，解析器就已经通过一个名为函数声明提升（function declaration hoisting）的过程，读取并将函数声明添加到执行环境中。对代码求值时，JavaScript引擎在第一遍会声明函数并将它们放到源代码树的顶部。所以，即使声明函数的代码在调用它的代码后面，JavaScript引擎也能把函数声明提升到顶部。如果像下面例子所示的，把上面的函数声明改为等价的函数表达式，就会在执行期间导致错误。
```
alert(sum(10,10));
var sum = function(num1, num2){
    return num1 + num2;
};
```
以上代码之所以会在运行期间产生错误，原因在于函数位于一个初始化语句中，而不是一个函数声明。换句话说，在执行到函数所在的语句之前，变量sum中不会保存有对函数的引用；而且，由于第一行代码就会导致“unexpected identifier”（意外标识符）错误，实际上也不会执行到下一行。

## 作为值的函数
因为ECMAScript中的函数名本身就是对象，所以函数也可以作为值来使用。也就是说，不仅可以像传递参数一样把一个函数传递给另一个函数，而且可以将一个函数作为另一个函数的结果返回。来看一看下面的函数。
```
function callSomeFunction(someFunction, someArgument){
    return someFunction(someArgument);
}
```
这个函数接受两个参数。第一个参数应该是一个函数，第二个参数应该是要传递给该函数的一个值。然后，就可以像下面的例子一样传递函数了。
```
function add10(num){
    return num + 10;
}

var result1 = callSomeFunction(add10, 10);
alert(result1);   //20

function getGreeting(name){
    return 'Hello, ' + name;
}

var result2 = callSomeFunction(getGreeting, 'Nicholas');
alert(result2);   //'Hello, Nicholas'
```
这里的callSomeFunction()函数是通用的，即无论第一个参数中传递进来的是什么函数，它都会返回执行第一个参数后的结果。还记得吧，要访问函数的指针而不执行函数的话，必须去掉函数名后面的那对圆括号。因此上面例子中传递给callSomeFunction()的是add10和getGreeting，而不是执行它们之后的结果。

当然，可以从一个函数中返回另一个函数，而且这也是极为有用的一种技术。例如，假设有一个对象数组，我们想要根据某个对象属性对数组进行排序。而传递给数组sort()方法的比较函数要接收两个参数，即要比较的值。可是，我们需要一种方式来指明按照哪个属性来排序。要解决这个问题，可以定义一个函数，它接收一个属性名，然后根据这个属性名来创建一个比较函数，下面就是这个函数的定义。
```
function createComparisonFunction(propertyName) {

    return function(object1, object2){
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];

        if (value1 < value2){
            return -1;
        } else if (value1 > value2){
            return 1;
        } else {
            return 0;
        }
    };
}
```
这个函数定义看起来有点复杂，但实际上无非就是在一个函数中嵌套了另一个函数，而且内部函数前面加了一个return操作符。在内部函数接收到propertyName参数后，它会使用方括号表示法来取得给定属性的值。取得了想要的属性值之后，定义比较函数就非常简单了。

## 函数的内部属性
函数的内部属性可以理解为函数的内部变量，可以在函数体内直接访问，不同于函数对象的属性，需要函数对象的引用进行访问；

1、arguments属性

arguments在前面介绍过，它是一个类数组对象，包含着传入函数中的所有参数。虽然arguments的主要用途是保存函数参数，但这个对象还有一个名叫callee的属性，该属性是一个指针，指向拥有这个arguments对象的函数。请看下面这个非常经典的阶乘函数。
```
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * factorial(num-1)
    }
}
```
定义阶乘函数一般都要用到递归算法；如上面的代码所示，在函数有名字，而且名字以后也不会变的情况下，这样定义没有问题。但问题是这个函数的执行与函数名factorial紧紧耦合在了一起。为了消除这种紧密耦合的现象，可以像下面这样使用arguments.callee。
```
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1)
    }
}
```
在这个重写后的factorial()函数的函数体内，没有再引用函数名factorial。这样，无论引用函数时使用的是什么名字，都可以保证正常完成递归调用。例如：
```
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1)
    }
}

var trueFactorial = factorial;

factorial = function(){
    return 0;
};

alert(trueFactorial(5));     //120
alert(factorial(5));         //0
```

2、 this属性

除了arguments属性外，函数内部的另一个特殊对象是this，其行为与Java和C#中的this大致类似。换句话说，this引用的是调用函数的环境对象（在全局作用域中调用函数的对象就是window对象）。来看下面的例子。
```
window.color = 'red';
var o = { color: 'blue' };

function sayColor(){
    alert(this.color);
}

sayColor();     //'red'

o.sayColor = sayColor;
o.sayColor();   //'blue'
```
函数的名字仅仅是一个包含指针的变量而已。因此，即使是在不同的环境中执行，全局的sayColor()函数与o.sayColor()指向的仍然是同一个函数。

在函数中this指向调用函数的环境对象，但是在对象中，this指向的是当前对象；


## 函数对象的属性和方法

1、caller属性

ECMAScript 5也规范化了另一个函数对象的属性：caller。除了Opera的早期版本不支持，其他浏览器都支持这个ECMAScript 3并没有定义的属性。这个属性中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为null。例如：
```
function outer(){
    inner(); 
}

function inner(){
    alert(inner.caller);
}

outer();
```
以上代码会导致警告框中显示outer()函数的源代码。因为outer()调用了inter()，所以inner.caller就指向outer()。为了实现更松散的耦合，也可以通过arguments.callee.caller来访问相同的信息。
```
function outer(){
    inner();
}

function inner(){
    alert(arguments.callee.caller);
} 

outer();
```

2、length属性

前面曾经提到过，ECMAScript中的函数是对象，因此函数也有属性和方法。每个函数都包含两个属性：length和prototype。其中，length属性表示函数希望接收的命名参数的个数，如下面的例子所示。
```
function sayName(name){
    alert(name);
}      

function sum(num1, num2){
    return num1 + num2;
}

function sayHi(){
    alert('hi');
}

alert(sayName.length);      //1
alert(sum.length);          //2
alert(sayHi.length);        //0
```
以上代码定义了3个函数，但每个函数接收的命名参数个数不同。首先，sayName()函数定义了一个参数，因此其length属性的值为1。类似地，sum()函数定义了两个参数，结果其length属性中保存的值为2。而sayHi()没有命名参数，所以其length值为0。

在ECMAScript核心所定义的全部属性中，最耐人寻味的就要数prototype属性了。对于ECMAScript中的引用类型而言，prototype是保存它们所有实例方法的真正所在。换句话说，诸如toString()和valueOf()等方法实际上都保存在prototype名下，只不过是通过各自对象的实例访问罢了。在创建自定义引用类型以及实现继承时，prototype属性的作用是极为重要的（将在后面详细讲解）。在ECMAScript 5中，prototype属性是不可枚举的，因此使用for-in无法发现。

3、apply()方法

apply()方法的用途是在特定的作用域中调用函数，实际上等于设置函数体内this对象的值。首先，apply()方法接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组。其中，第二个参数可以是Array的实例，也可以是arguments对象。例如：
```
function sum(num1, num2){
    return num1 + num2;
}

function callSum1(num1, num2){
    return sum.apply(this, arguments);        // 传入arguments对象
}

function callSum2(num1, num2){
    return sum.apply(this, [num1, num2]);    // 传入数组
}

alert(callSum1(10,10));   //20
alert(callSum2(10,10));   //20
```
在上面这个例子中，callSum1()在执行sum()函数时传入了this作为this值（因为是在全局作用域中调用的，所以传入的就是window对象）和arguments对象。而callSum2同样也调用了sum()函数，但它传入的则是this和一个参数数组。这两个函数都会正常执行并返回正确的结果。

4、call()方法
call()方法与apply()方法的作用相同，它们的区别仅在于接收参数的方式不同。对于call()方法而言，第一个参数是this值没有变化，变化的是其余参数都直接传递给函数。换句话说，在使用call()方法时，传递给函数的参数必须逐个列举出来，如下面的例子所示。
```
function sum(num1, num2){
    return num1 + num2;
}

function callSum(num1, num2){
    return sum.call(this, num1, num2);
}

alert(callSum(10,10));   //20
```
在使用call()方法的情况下，callSum()必须明确地传入每一个参数。结果与使用apply()没有什么不同。至于是使用apply()还是call()，完全取决于你采取哪种给函数传递参数的方式最方便。如果你打算直接传入arguments对象，或者包含函数中先接收到的也是一个数组，那么使用apply()肯定更方便；否则，选择call()可能更合适。（在不给函数传递参数的情况下，使用哪个方法都无所谓。）

事实上，传递参数并非apply()和call()真正的用武之地；它们真正强大的地方是能够扩充函数赖以运行的作用域。下面来看一个例子。
```
window.color = 'red';
var o = { color: 'blue' };

function sayColor(){
    alert(this.color);
}

sayColor();                //red

sayColor.call(this);       //red
sayColor.call(window);     //red
sayColor.call(o);          //blue
```
这一次，sayColor()也是作为全局函数定义的，而且当在全局作用域中调用它时，它确实会显示'red'——因为对this.color的求值会转换成对window.color的求值。而sayColor.call(this)和sayColor.call(window)，则是两种显式地在全局作用域中调用函数的方式，结果当然都会显示'red'。但是，当运行sayColor.call(o)时，函数的执行环境就不一样了，因为此时函数体内的this对象指向了o，于是结果显示的是'blue'。

5、bind()方法

ECMAScript 5还定义了一个方法：bind()。这个方法会创建一个函数的实例，其this值会被绑定到传给bind()函数的参数。例如：
```
window.color = 'red';
var o = { color: 'blue' };

function sayColor(){
    alert(this.color);
} 
var objectSayColor = sayColor.bind(o);
objectSayColor();    //blue
```
在这里，sayColor()调用bind()并传入对象o，创建了objectSayColor()函数。objectSayColor()函数的this值等于o，因此即使是在全局作用域中调用这个函数，也会看到'blue'。

6、其他继承的方法

每个函数继承的toLocaleString()和toString()方法始终都返回函数的代码。返回代码的格式则因浏览器而异——有的返回的代码与源代码中的函数代码一样，而有的则返回函数代码的内部表示，即由解析器删除了注释并对某些代码作了改动后的代码。由于存在这些差异，我们无法根据这两个方法返回的结果来实现任何重要功能；不过，这些信息在调试代码时倒是很有用。另外一个继承的valueOf()方法同样也只返回函数代码。
