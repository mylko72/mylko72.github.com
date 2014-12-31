---
layout: post
title: "Javscript this, call, apply, bind"
description: ""
category: javascript
tags: []
---
{% include JB/setup %}

##Method, Function

먼저 메소드와 함수의 차이에 대해서 간단히 알아보자. ES5 Spec 에 의하면 메소드는 Object 에 붙어있는 Function 이다. new 로 Object 를 생성하고 이 오브젝트의 Method 내에서 this 는 해당 오브젝트의 인스턴스를 가리킨다. (자바스크립트에서는 클래스가 따로 없으므로 오브젝트라 부르는 것 같다.). 반면 Method 가 아니라 Function 내부에서 this 는 window, Node.js 라면 global 이다. 그리고 strict mode 라면 Function 내부에서 this 는 undefined 다.

요약하자면 아무데에도 붙어있지 않고 홀로 정의되어 있는 것은 함수요, 오브젝트의 프로퍼티로 정의된 함수는 메소드이며,  함수 내에서 this 는 global, window 또는 undefined 가 될 수 있다. 반면 오브젝트의 메소드 내에서 this 는 해당 오브젝트의 인스턴스를 가리킨다. 

##Self

아래는 흔히 하는 실수와, 변수 `self` 를 통해서 해결하는 방법이다.

```javascript
function Shape() {
  this.x = 0;
  this.y = 0;
}

Shape.prototype = {
  move: function(x, y) {
    this.x += x;
    this.y += y;

    function checkBounds() {
      if (this.x > 100) {
        console.error('Warning: Shape out of bounds');
      }
    }

    checkBounds();
  }
};

var shape = new Shape();
shape.move(101, 1);
```

`console.error` 가 호출되지 않는 이유는, `checkBounds` Function 은 `Shape.move` property 내에서 정의되어 있지만, 그 자체가 Object 의 메소드는 아니기 때문이다.

```javascript
Shape.prototype = {
  move: function(x, y) {
    var self = this;

    this.x += x;
    this.y += y;

    function checkBounds() {
      if (self.x > 100) {
        console.error('Warning: Shape out of bounds');
      }
    }

    checkBounds();
  }
};
```

아무리 Object 에 붙어있는 Method 라도, 그 자체로 따로 변수로 저장하면 단순한 Function 이다. 무슨말인고 하니

```javascript
this.x = 9; 
var module = {
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 81

var getX = module.getX;
getX(); // 9, because in this case, "this" refers to the global object
```

`getX` 는 단순한 Function 이다. `new Contsructor` 로 `Instance` 를 만들고, `Instance.method()` 와 같이 호출하거나, `Object.method()` 처럼 호출되는 것이 아니면 해당 함수는 전역 컨텍스트에서 실행되므로 `this` 도 `global` 이거나 `window` 일거다. 