# MVC和类

========

## 什么是MVC
MVC是一种设计模式，它将应用划分为3个部分：数据层（模型），展现层（视图）和用户交互层（控制器）。

用户事件的发生是这样的过程：

1. 用户和应用产生交互
2. 控制器的事件处理器被触发
3. 控制器从模型中请求数据，并将其交给视图
4. 视图将数据层现给用户

## 模型
模型用来存放应用的所有数据对象，它只需包含数据及直接和这些数据相关的逻辑。同时模型实例应该是面向对象的。

不要这样：

	var user = users["foo"];
	destoryUser(user);

而要这样：

	var user = User.find("foo");
	user.destory();

## 视图
视图是层现给用户的，用户与之产生交互。视图大都是由HTML、CSS和Javascript组成。除了模板中简单的条件语句，视图中不应当包含任何其他逻辑（所以其实React的组件被说成是视图层并不准确）。

不要这样：

	//template.html
	<div>
		<script>
			function formatDate(date) {
				/* …… */
			}
		</script>
		${ formatDate(this.date)}
	</div>

而要这样：
	
	//helper.js
	var helper = {};
	helper.formatDate = function () {};

	//template.html
	<div>
		${ helper.formatDate(this.date) }
	</div>

## 控制器
控制器是模型和视图之间的纽带。控制器从视图获得事件和输入，对它们进行处理，并相应地更新视图。

## 创建类

	var Person = function (name) {
		this.name = name;
	};

	//实例化一个Person
	var alice = new Person('alice');

## 给类添加函数
	
	Person.find = function (id){};

	var person = Person.find(1);

## 给构造函数添加实例函数
	
	Person.prototype.breath = function () {};

	var person = new Person;

## 基于原型的类继承
Javascript是基于原型的编程语言，原型用来区别类和实例。原型是一个“模板”对象，它上面的属性被用做初始化一个新对象。任何对象都可以作为另一个对象的原型对象，以此来共享属性。实际上，可以将其理解为某种形式的继承。

Javascript读取属性的过程是：

1. 本地对象查找属性
2. 若没找到，在对象的原型中查找
3. 若仍未找到，还会继续查找原型的原型
4. 最后找到`Object.prototype`，若找到这个值，返回`undefined`

基于原型的类继承事例如下：

	var Animal = function () {};
	
	Animai.prototype.breath = function () {
		console.log("breath");
	};
	
	var Dog = function () {};
	
	Dog.prototype = new Animal;
	
	Dog.prototype.wag = function () {
		console.log("wag tail");
	};
	
## 函数调用
函数和其他一样都是对象。然而，和其他对象不同的是，函数是可调用的。函数内上下文，如`this`的取值，取决于调用它的位置和方法。

函数调用的几种方法：

- 使用括号调用
- apply()
- call()

`apply`和`call`的主要功能就是更改函数执行的上下文，同时它俩的区别是前者传递数组，后者传递参数序列。

## 添加私有函数
利用匿名函数创建私有作用域，这些私有作用域只能在内部访问(ES6有了`let`和`{}`有不用这样了)。具体做法如下：

	var Person = function () {};
	
	(function() {
		var findById = function () {};
		
		Person.find = function (id) {
			if (typeof id == 'interger')
				return findById(id);
		};
	})();

不用`var`定义变量会使之成为全局变量，正确的定义一个全局变量的方法是：
	
	(function(exports){
		var foo = "bar";
		
		exports.foo = foo;
	})(window);