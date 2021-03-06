---
layout: single
title: "Node의 events를 알아보자."

categories: node
tags: events
typora-copy-images-to: ../images/2021-05-24
---

### 1. node의 **events** 는 어떻게 만들어져 있지?

```javascript
function Emitter() {
  this.events = {};
}

/**
 * @param {*} listener  The code that responds to an event.
 */
Emitter.prototype.on = function (type, listener) {
  this.events[type] = this.events[type] || [];
  this.events[type].push(listener);
};

Emitter.prototype.emit = function (type) {
  if (this.events[type]) {
    this.events[type].forEach((listener) => {
      listener();
    });
  }
};

module.exports = Emitter;
```

### 2. events를 상속해서 사용해보자.

- events를 상속받은 Greeter 생성.

```javascript
const Events = require("events");

module.exports = function Greeter() {
  // inherits는 prototype method만 상속시키므로
  // 모든 property, method 상속을 위해 this를 Events.call
  Events.call(this);
  this.greeting = "Hello world!";
};

util.inherits(Greeter, Events);

Greeter.prototype.greet = function (data) {
  console.log(this.greeting, data);
  this.emit("greet", data);
};
```

- 사용

```javascript
const Greeter = require("./Greeter");
const greeter = new Greeter();

greeter1.on("greet", function (data) {
  console.log("someone greeted!", data);
});

greeter.greet("KHC");
```

### 3. Class Version

```javascript
const Events = require("events");

module.exports = class Greeter extends Events {
  constructor() {
    super(); // inherit all property and method.
    this.greeting = "Hellow world!";
  }
  greet(data) {
    console.log(this.greeting, data);
    this.emit("greet", data);
  }
};
```

### 4. Prototype Chain Image

<img src="{{ site.url }}{{ site.baseurl }}/images/2021-05-24/node-events.png" alt="node-events" style="zoom: 67%;" />
