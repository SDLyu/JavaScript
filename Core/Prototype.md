# 原型 (Prototype)

每個 JavaScript 物件都有一個原型特性。 
在了解原型鍊及原型繼承前，先來了解一下 JavaScript 創建物件的方法以及各方法的原型。

## 物件創建方法

1. 物件字面值的原型

所以用物件字面值創建的物件"都有同一個原型物件"，`Object.prototype`。

2. `new` 運算子與建構子創建的物件原型

```javascript
function A(){
	this.x = 0;
}

A.prototype = {
	y : function(){}
}
```

3. `Object.creat` 方法

Object.creat()的第一個引數為新創物件的原型

```javascript
B.prototype = Object.create(A.prototype);
```

## 原型鍊 (Prototype chain)

當我們對一個物件查找某個屬性時：

1. JavaScript 運行環境首先看這個物件本身有沒有這個方法，如果有就直接調用。
2. 如果沒找到，就查找這個物件的 `__proto__` 屬性指向的物件（就是前面說的該物件的建構函式的 `prototype` 屬性），如果有該方法就調用。 
3.	沒有的話繼續查找 `__proto__` 屬性指向物件的 `__proto__` 屬性指向的物件。
4.	以此類推，直到最後，找到 JavaScript 所有的物件都會指向預設指向的 Object（JavaScript 的根物件）。

當我們對一個物件的屬性進行 **賦值** 時：

1. 如果該物件本身 **有** 這個屬性, 則對該屬性賦值。
2. 如果該物件本身 **没有** 這個屬性, 則在該物件上新建一個該屬性，並進行賦值。（這時 **不會** 通過 `__proto__` 屬性，找該物件的原型並檢查有没有該屬性)

我們可以推出以下兩點：

1. 屬性讀取時，返回 **最先找到的屬性值**。
2. 屬性賦值時，如果物件自身不存在該屬性，則創建該的屬性。

## JavaScript 的原型繼承

物件系統的繼承特性，有三種實現方式：

+ 基於類（class-based）
+ 基於原型（prototype-based）
+ 基於原類（metaclass-based）

JavaScript 屬於第二類，基於原型的繼承。

```javascript
function parent(){
	//...
}

parent.prototype.parentcall = function(){
	cosole.log("parent!")
}

function child(){
	//...
}

child.prototype = new parent();
child.prototype.constructor = child;
```

將 `child` 類別的 `prototype` 屬性指向父類別的實體，讓 `child` 繼承 `parent`，由於子類別的 `prototype` 屬性已經完全指向父類別實體，所以要手動恢復。

```javascript
var peter = new child();
peter.parentcall();  // output parent!
```

創建出 `peter` 後呼叫 `parentcall()` 方法。由於 `child` 裡沒有 `parentcall` 方法，所以會沿著原型鍊查找到 `parent` 的 `parentcall()`。藉由原型鍊查找的方式來達到繼承。