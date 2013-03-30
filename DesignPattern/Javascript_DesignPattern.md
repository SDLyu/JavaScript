# 理解JavaScript設計模式

今天，我們要介紹一些常見的設計模式。</br>
設計模式提供開發者一些複用及優雅的方法解決技術上的問題。</br>
想要變成一個更好的JavaScript開發者嗎? 那就接著讀下去吧！

## 介紹

紮實的設計模式是建造可維護軟體應用程式的基本元素。如果你參加過科技面試，你可能被問過關於它們的問題。在這篇教學中，我們會看一些你能馬上使用的模式。

## 什麼是設計模式(Design Patter)?

> A design pattern is a reusable software solution.</br>
> 一個設計模式是一個可複用的軟體解決方法。

簡單來說，一個設計模式是一個可複用的軟體解決方案，在軟體開發時，去解決特定且時常發生的問題。在多年的軟體開發實踐中，專家們已經找到了解決類似問題的方法。這些解決方案已經封裝成設計模式。所以：

+ 模式是行之有效的，它是解決軟件開發問題的方法
+ 模式是可擴展的，因為他們通常是結構化的並且有一套你應該遵循的規則
+ 對於一些類似的問題，模式是可複用的

在下面的教學中，我們將會引進一些設計模式的例子。

## 設計模式的類型

在軟體開發中，設計模式通常依功能或目的可被區分出幾類。在此教學中，我們將涵蓋最重要的三大類，下面是簡單的介紹：

1. **創建型**設計模式著重於物件創建或類別創建。這聽起來似乎很簡單，但是對一個大型應用程式來說，物件的創建過程是需要被控制。

2. **結構型**設計模式著重於管理物件之間的關係，讓你的應用程式能較有彈性的建構。一個重要的觀點是，當部份改變發生時，確保對其它部份不會造成影響。

3. **行為型**設計模式著重於物件之間的溝通。

你在讀完簡單的介紹後或許仍有些疑問。這是一定的，一但我們更深入探討接下來的設計模式，事情會變明朗的。那麼，開始吧！

## 關於JavaScript的類別(Classes)

閱讀設計模式相關資料時，你會發現它經常涉及到類別(classes)和物件(objects)。這令人感到疑惑，因為JavaScript並沒有真正的"類別"結構，更正確的說法是"資料型別(data type)"。

### JavaScript的資料型別

JavaScript是一種物件導向的語言，物件繼承的概念被稱為原型繼承。藉由定義構造函式(constructor function)，資料型別可以被創建，比如：

	function Person(config) {
		this.name = config.name;
		this.age = config.age;
	}
	Person.prototype.getAge = function() {
		return this.age;
	};
	var tilo = new Person({name:"Tilo", age:23 });
	console.log(tilo.getAge());

當定義`Person`上的方法時，注意`prototype`的使用。因為多個`Person`物件將引用相同的原型，這種寫法允許`getAge()`分享給所有的`Person`實例，而不是為每個實例重新定義`getAge()`。另外，任何繼承`Person`的資料型別將有能力訪問`getAge()`。

### 處理隱私

另一個常見的JavaScript議題是沒有實質上的私有變數，然而，我們可以使用閉包(closure)模擬私有變數。考慮下面的程式碼：

	var retinaMacbook = (function() {
		//Private variables
		var RAM, addRAM;
		RAM = 4;
		//Private method
		addRAM = function (additionalRAM) {
			RAM += additionalRAM;
		};
		return {
			//Public variables and method 
			USB: undefined,
			insertUSB: function (device) {
				this.USB = device;
			},
			removeUSB: function () {
				var device = this.USB;
				this.USB = undefined;
				return device;
			}
		};
	})();

上面的範例中，我們創建一個`retinaMacbook`物件，利用公開和私有變數及方法。我們可以這樣使用它：

	retinaMacbook.insertUSB("myUSB");
	console.log(retinaMacbook.USB); //logs out "myUSB"
	console.log(retinaMacbook.RAM) //logs out undefined

在JavaScript中，我們可以利用函式和閉包做很多事，但我們不會在此教學中提到。藉由資料型別和私有變數的小小教學，我們可以帶上它來學習設計模式。

## 創建型設計模式

有許多不同種類的創建型設計模式，但我們在此教學只會涵蓋兩項：建造者模式和原型模式。我發現這些常被使用且值得注意。

### 建造者模式(Builder Pattern)

建造者模式經常被使用在網頁開發上，而且你在未了解建造者模式之前可能已經使用過了。簡單的說，這個模式的定義如下：

> Applying the builder pattern allows us to construct objects by only specifying the type and the content of the object. We don’t have to explicitly create the object.</br>
> 應用建造者模式，允許我們只由指定物件的類型和內容構造物件。我們不必顯式地創建物件。

例如，您可能已經在jQuery中這樣做了無數次：

	var myDiv = $('<div id="myDiv">This is a div.</div>');  
	//myDiv now represents a jQuery object referencing a DOM node.  
	var someText = $('<p/>');  
	//someText is a jQuery object referencing an HTMLParagraphElement  
	var input = $('<input />');  

看一下上面的三個例子。第一個，我們傳入一個`<div/>`元素及一些內容。第二個，我們傳入空的`<p/>`標籤。最後，我們傳入一個`<input />`元素。三個結果是相同的：我們回傳一個參考到DOM節點的jQuery物件。</br></br>
在jQuery中，`$`採用建造者模式。在每個例子中，我們回傳一個jQuery的DOM物件和jQuery library提供的所有方法，但我們不用顯式地調用`document.createElement`。真正原因是，JavaScript library都已經處理好了。</br></br>
想像一下，如果我們要顯式地創建DOM元素並且在元素中插入內容那是需要花多大的功夫啊！藉由利用建造者模式，我們能夠專注在物件型別和內容上，而不是顯式地創造它。

### 原型模式(Prototype Pattern)

在此之前，我們介紹在JavaScript中，透過函式和新增方法到物件的`prototype`屬性上來定義資料型別。原型模式允許物件透過其它物件的原型完成繼承。

> The prototype pattern is a pattern where objects are created based on a template of an existing object through cloning.</br>
> 原型模式是物件的創建模式，基於透過複製一個現有的物件模板。

在JavaScript中，這是一個簡單且自然地實現繼承的方法。例如：

	var Person = {
		numFeet: 2,
		numHeads: 1,
		numHands:2
	};
	//Object.create takes its first argument and applies it to the prototype of your new object.
	var tilo = Object.create(Person);
	console.log(tilo.numHeads); //outputs 1
	tilo.numHeads = 2;
	console.log(tilo.numHeads) //outputs 2

`Person`物件的屬性(方法)會應用到`tilo`物件的原型上。如果我們希望它們是不同的，我們可以重新定義`tilo`物件上的屬性。</br></br>
在上面的例子中，我們使用`Object.create()`。但是，Internet Explorer 8 不支持較新的方法。在這種情況下，我們可以模擬它的行為：

	var vehiclePrototype = {
		init: function (carModel) {
			this.model = carModel;
		},
		getModel: function () {
			console.log( "The model of this vehicle is " + this.model);
		}
	};
	function vehicle (model) {
		function F() {};
		F.prototype = vehiclePrototype;
		var f = new F();
		f.init(model);
		return f;
	}
	var car = vehicle("Ford Escort");
	car.getModel();

這種方法唯一的缺點是你不能指定唯讀屬性，當使用`Object.create())`時，**可以指定使用**。不過，原型模式可以呈現物件是如何繼承其他物件。

## 結構型設計模式

想弄清楚系統運作，結構型設計模式是相當有用的。它讓我們的應用程式易於擴展並保持維護性。接下來我們看看這類模式：組合模式和門面模式。

### 組合模式(Composite Pattern)

組合模式是另一種在沒有任何了解之前你可能已經使用過的模式。

> The composite pattern says that a group of objects can be treated in the same manner as an individual object of the group.
> 組合模式說，一組物件可以被視為本集團作為一個單獨的對象以同樣的方式。

所以，這是什麼意思呢? 看一下這個jQuery的範例(大多數JavaScript libraries有相等的語法)：

	$('.myList').addClass('selected');
	$('#myItem').addClass('selected');
	//dont do this on large tables, it's just an example.
	$("#dataTable tbody tr").on("click", function(event){
		alert($(this).text());
	});
	$('#myButton').on("click", function(event) {
		alert("Clicked.");
	});

大部分的JavaScript libraries提供一個統一的API，不管我們是否正在處理一個DOM元素或DOM元素的陣列。在第一個例子中，我們可以添加`selected`標籤到所有被`.myList`選擇器所挑出的項目，但我們可以用同樣的方法，處理一個單一DOM元素`#myItem`。同樣，透過相同的API我們可以使用`on()`方法將事件處理器綁定到多個或單個節點上。</br></br>
通過利用複合模式，jQuery(和許多其他libraries)為我們提供了一個簡化的API。</br></br>
組合模式有時也會導致問題。在一個鬆散類型的語言，如JavaScript，了解自已是否正在處理一個單一的元素或多個元素，經常是有幫助的。由於組合模式都使用相同的API，我們可能把單一個誤認成其它多個，最終導致不可預期的錯誤。一些libaries，如YUI3，提供兩個不同獲取元素的方法(`Y.one()` VS `Y.all()`)。

### 門面模式(Facade Pattern)

下面是另一種常見的模式，我們認為是理所當然。事實上，這是我的最愛的模式之一，因為它簡單，我已經看到門面模式被使用在，幫助所有瀏覽器間不一致性的地方。這裡解釋了什麼是門面模式：

> The Facade Pattern provides the user with a simple interface, while hiding it’s underlying complexity.</br>
> 門面模式為使用者提供了一個簡單的界面，同時隱藏它的潛在的複雜性。

門面模式總是提高了軟體中的可用性。再次使用jQuery作為一個例子，`ready()`方法是library中比較普及的方法之一：

	$(document).ready(function() {  
		//all your code goes here...  
	});

`ready()`方法實際上實現了一個門面。如果你看一下原始程式，你會發現：

	ready: (function() {
	...
		//Mozilla, Opera, and Webkit
		if (document.addEventListener) {
			document.addEventListener("DOMContentLoaded", idempotent_fn, false);
			...
		}
		//IE event model
		else if (document.attachEvent) {
			// ensure firing before onload; maybe late but safe also for iframes
			document.attachEvent("onreadystatechange", idempotent_fn) 
			// A fallback to window.onload, that will always work
			window.attachEvent("onload", idempotent_fn);
			...
		}
	})

詳細地來看，`ready()`不是這麼簡單，jQuery標準化了瀏覽器間的不一致，以確保`ready()`在適當的時間被觸發。然而，作為一個開發者，你只要透過一個簡單的介面就能呈現。</br></br>
大多數門面模式的例子遵循這一原則。實現時，本質上我們通常依賴於條件語句，但它提供一個簡單的介面給用戶呈現。其它實現這種模式的方法包括`animate()`和`css()`。你能想想為什麼它們要使用門面模式呢?

## 行為型模式

任何物件導向的軟體系統，物件間的交流是不可避免的。不組織，交流，會導致難以發現和修復的臭蟲。行為型設計模式規定了不同的方法來組織物件之間的交流。在這一節，我們要介紹觀察者模式和中介者模式。

### 觀察者模式(Observer Pattern)

觀察者模式是第一個我們要講解的行為型模式。它是：

> In the Observer Pattern, a subject can have a list of observers that are interested in it’s lifecycle. Anytime the subject does something interesting, it sends a notification to its observers. If an observer is no longer interested in listening to the subject, the subject can remove it from its list.</br>
> 在觀察者模式中，一個主體在它的生命週期，可以有一個觀察者列表。任何時候，主體做的一些有趣的事情，它會發送一個通知到觀察者。如果一個觀察者對主體不再有興趣，主體可以從列表中刪除它。

聽起來相當簡單，不是嗎? 我們需要三個方法來描述這個模式：

+ publish(data): 被主體呼叫，當它有一個通知產生時。一些資料會由這個方法傳遞。
+ subscribe(observer): 被主體呼叫，將一個觀察者加入至觀察者列表。
+ unsubscribe(observer): 被主體呼叫，將一個觀察者從觀察者列表移除。

好了，事實證明，最新式的JavaScript libraries支持這三種方法作為自定義事件基礎的一部分。通常，它們有一個`on()`或`attach()`方法，一個`trigger()`或`fire()`方法和一個`off()`或`detach()`方法。考慮下面的程式碼:

	//We just create an association between the jQuery events methods

 </p>

	//and those prescribed by the Observer Pattern but you don't have to.
	var o = $( {} );
	$.subscribe = o.on.bind(o);
	$.unsubscribe = o.off.bind(o);
	$.publish = o.trigger.bind(o);
	// Usage
	document.on( 'tweetsReceived', function(tweets) 
		//perform some actions, then fire an even
		$.publish('tweetsShow', tweets)
	})
	//We can subscribe to this event and then fire our own event
	$.subscribe( 'tweetsShow', function() 
		//display the tweets someho
		.
		//publish an action after they are shown
		$.publish('tweetsDisplayed);
		});
	$.subscribe('tweetsDisplayed, function() {
		...
	});

觀察者模式是一個較簡單的模式實作，但它很強大。JavaScript是非常適合採用這種模式，因為它是基於事件(event-based)的語言。下次當你開發網頁應用程式時，思考一下模組間的鬆耦合並採用觀察者模式作為一種交流手段。如果太多的主體和觀察者加入，觀察者模式可能會有問題。這可能發生在大型系統中，下一個模式試圖解決這個問題，讓我們來看看。

### 中介者模式(Mediator Pattern)

我們要介紹的最後一個模式是中介者模式。它與觀察者模式相似，但有一些明顯的差異。

> The Mediator Pattern promotes the use of a single shared subject that handles communication with multiple objects. All objects communicate with each other through the mediator.</br>
> 中介者模式提倡使用一個單一的共享主體，處理多個物件間的交流。所有的物件彼此交流都通過中介者。

現實世界中一個不錯的比喻是，一個空中交通塔，處理機場和航班之間的通信。在軟體開發的世界裡，中介者模式經常用在一個系統過份複雜時。藉由加入中介者，交流可以透過單一物件處理，而不是具有相互交流的多個物件。在意義上，中介者模式可用於替換實現了觀察者模式的系統。</br></br>
Addy Osmani在this gist裡有一個簡化的中介者模式實作。讓我們來談談你該如何使用它。想像你有一個網頁應用程式，它允許使用者點擊專輯，然後播放音樂。你應該建立一個中介者像這樣：

	$('#album').on('click', function(e) {
		e.preventDefault();
		var albumId = $(this).id();
		mediator.publish("playAlbum", albumId);
	});
	var playAlbum = function(id) {
		…
		mediator.publish("albumStartedPlaying", {songList: [..], currentSong: "Without You"});
	};
	var logAlbumPlayed = function(id) {
		//Log the album in the backend
	};
	var updateUserInterface = function(album) {
		//Update UI to reflect what's being played
	};
	//Mediator subscriptions
	mediator.subscribe("playAlbum", playAlbum);
	mediator.subscribe("playAlbum", logAlbumPlayed);
	mediator.subscribe("albumStartedPlaying", updateUserInterface);

與觀察者模式相比，這個模式的好處是一個單一物件負責交流，而觀察者模式中多個物件可以相互監聽和訂閱。</br></br>
在觀察者模式中，沒有一個封裝約束的物件。相反的，觀察者與主體必須合作以維持此約束。通訊模式是由觀察者和主體互連決定的：一個單一的主體通常有很多觀察者，有時一個主體的觀察者是另一個觀察者的主體(the observer of one subject is a subject of another observer)。

## 結論

> Someone has already applied it successfully in the past.</br>
> 在過去已經有人成功地應用它。

關於設計模式的偉大在於，在過去已經有人成功地應用它。有很多的開放式程式碼(open-source)在JavaScript中實現各種模式。作為開發人員，我們需要注意該在何時何地應用模式。我希望本教學回答的這些問題能幫助你更往前一步。

## 延伸閱讀

從這篇文章的大部分內容能在 Learning JavaScript Design Patterns 書裡找到，作者是Addy Osmani。這是一本線上書籍，免費且Creative Commons license下。這本書廣泛涵蓋了很多不同模式的理論和實現，無論是在原生JavaScript或各種JavaScript libraries。當你開始你的下一個專案時，我鼓勵你看看它並且作為參考。