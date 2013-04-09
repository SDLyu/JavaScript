# 變數物件(Variable Object)

+ 變數物件是與執行上下文相關的資料作用域(scope of data)。<br />
+ 它是與上下文關聯的特殊物件，用於存儲被定義在上下文中的變數(variables)和函式宣告(function declarations)。<br />

---

當我們在程式中定義一些函數和變數，接著用這些函數和變數來建構我們的系統。<br />
對於解譯器來說，它如何以及從哪裡找到這些資料的(函數，變數)?<br />
當引用一個物件的時候，在解譯器內部又發生了什麼?<br />

---

宣告新的變數和函數的過程其實就是在VO中創建新的變數以及函數名對應的屬性和屬性值的過程。<br />

	var a = 10; 
	function test(x) { var b = 20; }; 
	test(30);

上述代碼對應的變數物件則如下所示：

	VO(globalContext) = { a : 10, test : function };   // 全域上下文中的變數物件
	VO(test functionContext) = { x : 30, b : 20 };     //test函數上下文中的變數物件

在ECMAscript標準中定義的，變數物件只是一個抽象的概念。在實際執行上下文中，`VO`可能完全不叫`VO`，並且初始的結構也可能完全不同。

## 不同執行上下文中的變數物件

變數物件上的一些操作(比如：變數的初始化)和行為對於所有的執行上下文類型來說都已一樣的。從這一點來說，將變數物件表示成抽象的概念更加合適。
函數上下文還能定義額外的與變數物件相關的資訊。

### 全域上下文中的變數物件

+ 全域物件是一個在進入任何執行上下文前就創建出來的物件。
+ 全域物件以單例形式存在，它的屬性在程式任何地方都可以直接訪問，其生命週期隨著程式的結束而終止。

	全域物件在創建的時候，諸如`Math`,`String`,`Date`,`parseInt`等等屬性也會被初始化，同時，其中一些物件會指向全域物件本身，比如：DOM中，全域物件上的`window`屬性就指向了全域物件。

		global = { 
		  Math: , 
		  String: 
		  ... 
		  ... 
		  window: global 
		};

	在引用全域物件的屬性時，前缀通常可以省略，因為全域物件是不能通過名字直接訪問的。<br />
	然而，通過全域物件上的`this`值，以及通過如DOM中的`window`物件這樣遞迴引用的方式都可以訪問到全域物件：

		tring(10);     // 等同於 global.String(10); 
		// 加上前缀 
		window.a = 10; // === global.window.a = 10 === global.a = 10; 
		this.b = 20;   // global.b = 20;

	#### 全域上下文中的變數物件 = 全域物件本身

	理解這個事實是非常必要的，正是由於這個原因，當在全域上下文中聲明一個變數時，可以通過全域物件上的屬性來間接地引用該變數

		var a = new String('test'); 
		console.log(a);             // directly, is found in VO(globalContext): "test" 
		console.log (window['a']);  // indirectly via global === VO(globalContext): "test" 
		console.log (a === this.a); // true 
		var aKey = 'a'; 
		console.log (window[aKey]); // indirectly, with dynamic property name: "test"


### 函數上下文中的變數物件

+ 在函數的執行上下文中，VO是不能直接存取的。
+ 它主要扮演被稱作活化物件（Activation Object）（簡稱：AO）的角色。


## 變數物件(VO) v.s. 活化物件(AO)

### 變數物件定義

變數物件是與執行上下文相關的資料作用域(scope of data)。它是與上下文關聯的特殊物件，用於存儲被定義在上下文中的變數(variables)和函式宣告(function declarations)。

### 活化物件定義

當函數被呼叫，一個特殊的物件——活化物件(activation object) 將被創建。它包含形式參數(formal parameters) 與特殊參數(arguments)物件(具有索引屬性的形參鍵值表(map))。活化物件在函式上下文中作為變數物件使用。

### 變數物件與活化物件的區別

Variable Object也叫做Activation Object，因為有一些差異存在，所以規範中重新取一個名字以示區別，全域執行環境/eval執行環境中叫Variable Object，函數執行環境中就叫做Activation Object。

## 關於變數宣告

大多數講JavaScript的文章甚至是JavaScript的書通常都會這麼說："聲明全域變數的方式有兩種，一種是使用var關鍵字(在全域上下文中)，另外一種是不用var關鍵字(在任何位置)"。<br />
然而這樣的描述是錯誤的。要記住的是，*使用var關鍵字是聲明變數的唯一方式*。<br />
範例：

	a = 10;

僅僅是在全域物件上創建了新的屬性(而不是變數)。"不是變數"並不意味著它無法改變，它是ECMAScript中變數的概念(它之後可以變為全域物件的屬性，因為`VO(globalContext) === global)`。<br />
範例：

	console.log(a); // undefined, we know why 
	b = 10; 
	console.log(b); // 10, created at code execution 
	var a = 20; 
	console.log(a); // 20, modified at code execution

進入上下文時

	VO = { a: undefined };

我們看到，這個階段並沒有`b`，因為它不是變數，`b`在執行代碼階段才出現。<br />
這裡關於變數還有非常重要的一點：與簡單屬性不同的是，變數是不能刪除的，這意味著要想通過delete操作符來刪除一個變數是不可能的。<br />
範例：

	a = 10; 
	console.log(window.a); // 10 
	console.log (delete a); // true 
	console.log (window.a); // undefined 
	var b = 20; 
	console.log (window.b); // 20 
	console.log (delete b); // false 
	console.log (window.b); // still 20

## 實現ECMAScript標準的特性：__PARENT__屬性

正如此前介紹的，標準情況下，是無法直接訪問活化物件的。然而，在某些實現中，比如知名的SpiderMonkey和Rhino,函數有個特殊的屬性`__parent__`，該屬性是對該函數創建所在的活化物件的引用(或者全域變數物件)。<br />
如下所示(SpiderMonkey,Rhino)：

	var global = this; 
	var a = 10; 
	function foo() {} 
	alert(foo.__parent__); // global 
	var VO = foo.__parent__; 
	alert(VO.a); // 10 
	alert(VO === global); // true

上述例子中，可以看到函數`foo`是在全域上下文中創建的，相應的，它的 `__parent__` 屬性設置為全域上下文的變數物件，比如說：全域物件。
然而，在SpiderMonkey中以相同的方式獲取活化物件是不可能的：不同的版本表現都不同，內建函式的`__ parent__`屬性會返回`null`或者全域物件。在Rhino中，以相同的方式獲取活化物件是允許的。<br />
如下所示（Rhino）：

	var global = this; 
	var x = 10; 
	(function foo() { 
	  var y = 20; // the activation object of the "foo" context 
	  var AO = (function () {}).__parent__; 
	  print(AO.y); // 20 
	 
	  // __parent__ of the current activation 
	  // object is already the global object, 
	  // i.e. the special chain of variable objects is formed, 
	  // so-called, a scope chain 
	  print(AO.__parent__ === global); // true 
	  print(AO.__parent__.x); // 10 
	})();
