# 物件(Object)

+ 除了簡單型別(數字、字串、布林值、undefined、null)之外的型別都是物件。陣列是物件、函式是物件...等都是物件。<br />
+ 物件是特性的無序群集，每個特性都有名稱與值，可以想像成是雜湊(hash)、字典(dictionary)或關聯式陣列(associative array)。

## 物件創建

Javascript中，要創建物件有以下三種方法<br />

1. 物件字面值

	物件字面值是一串逗號分隔的"名稱 : 值"組合(冒號區隔)，並使用大括號包住。

		var A = {
			x : 0,
			y : function(){},
			z : {              //也能宣告成巢狀物件
				a : "",
				...
			}
		}

	物件字面值每次被估算時都會創建、初始化一個新的個別物件。<br />
	*缺點: 當要創建多個實體時很麻煩、代碼不能重用。*<br />

2. 使用`new`運算子

	`new`運算子，後面只能接function物件調用，透過這種方式調用的函式稱為建構子(constructor)。<br />

		function A () {
			...
		}
		var a = new A();

	核心JavaScript內建了原生型別用的建構式，比如:

		var o = new Object();     //創建空物件，等同於{}
		var a = new Array();      //創建空陣列，等同於[]
		var f = new Function();   //創建空函式，等同於function () {}
		...

3. ECMAscript5裡新增`Object.creat`方法

	`Object.creat()`可以用來新創一個物件，並用第一個引數作為該物件的原型，第二個引數非必須，用以描述新物件的特性。<br />

		var B = Object.creat(A);           //物件B繼承了物件A的特性
		var Z = Object.creat(Y, {x : 1});  //物件Z繼承了物件Y的特性，且有新特性x

	**使用`new`與`Object.creat()`實例化的差別:**

	使用`new`方法會實例化`A`，而使用`Object.creat`方法不會實例化`A`(即不會呼叫到`constroctor A()`)。這樣的好處在於可讓程式碼更清楚、簡單，能避免建構式被大量呼叫所造成的資源不足問題。


這三種方法是JavaScript中最基礎的創建方法，其它創建方法也都是從這三種衍伸的，比如:工廠方法。

## 查用物件的特性

Javascript中，要查用物件的特性有以下兩種方法<br />

1. 使用點運算子(`.`)

	使用點與識別字，與C或Java中存取結構(struct)或物件的靜態資料欄(static field)相似。

		object.property

	用.運算子來查用物件的特性時，特性的名稱是用識別字(identifier)表示，識別字必須逐字被鍵入Javascript程式中，所以程式無法操縱它們。<br />

2. 使用關聯式陣列(`[]`)

	看起來就像陣列存取，但是陣列的索引是字串而非數字。

		object["property"]

	用`[]`陣列記號來查用物件的特性，特性的名稱是用字串表示，字串是JavaScirpt的資料型別，所以程式執行時，它們可以被操作或創建。

	###使用[ ]配合字串我們可以彈性地存取物件的特性

		var A = {
			onAction1 : function (){
				console.log("Action1");
				},
			onAction2 : function (x){
				console.log(x);
			}
		}
		A["onAction" + 1]();          //output Action1
		A["onAction" + 1]("Action2"); //output Action2

## 創建與設置特性

要創建或設定特性，就使用跟查用時相同的`.`運算子或`[]`中括號，但它們要放在指定運算式的左邊。<br />
至於何時創建特性、何時設定特性，則視指定物件是否存在特性而定。<br />
如果指定的物件沒有設置特性，則在物件上創建特性。如果指定的物件已有特性則將該特性賦值。

	var book = {};    //宣告空物件
	book.edition = 6; //由於book物件沒有edition特性，則創建editin特性，並賦值6
	book.edition = 7; //設置edition的值為7

## 刪除物件的特性

`delete`運算子能從物件中移除特性。<br />
`delete`並非作用在值之上，而是作用在特性本身，比如：
	
	delete book.edition;    //book物件上的edition特性被刪除了
	delete book["title"];  //title的特性也沒了

