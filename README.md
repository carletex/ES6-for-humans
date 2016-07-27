# ES6 para humanos

<br>

### Tabla de contenidos

* [`let`, `const` y ámbito de bloques](#1-let-const-y-ámbito-de-bloques)
* [Funciones flecha](#2-funciones-flecha)
* [Parámetros de funciones por defecto](#3-parámetros-de-funciones-por-defecto)
* [Operador de propagación/soporte](#4-operador-de-propagaciónsoporte)
* [Extensión de objetos literales](#5-extensión-de-objetos-literales)
* [Literales Octales y Binarios](#6-literales-octales-y-binarios)
* [Desestructuración de Arrays y Objetos](#7-desestructuración-de-arrays-y-objetos)
* [super en Objetos](#8-super-en-objetos)
* [Plantillas de Literales y Delimitadores](#9-plantillas-de-literales-y-delimitadores)
* [for...of vs for...in](#10-forof-vs-forin)
* [Map y WeakMap](#11-map-y-weakmap)
* [Set y WeakSet](#12-set-y-weakset)
* [Clases en ES6](#13-clases-en-es6)
* [Symbol](#14-symbol)
* [Iteradores](#15-iteradores)
* [Generadores](#16-generadores)
* [Promesas](#17-promesas)

<br>

### 1. let, const y ámbito de bloques

`let` te permite hacer declaraciones ligadas a un bloque, lo que se conoce como ámbito de bloque. En vez de usar `var`, que proporciona un ámbito a nivel de función, se recomienda usar `let` en ES6.

```javascript
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
```

Otra manera de crear declaraciones en el ámbito de bloque, es usar `const`, lo cual declara constantes. En ES6, una `const` representa una referencia constante a un valor. Es decir, la asignación a la misma está bloqueado, pero el valor puede cambiar. Aquí tenemos un ejemplo simple:

```javascript
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // El valor es mutable
    console.log(ARR); // [3,6,7]
}
```

Unas cuantas cosas a tener en cuenta:

* El hoisting de `let` y `const` cambia con respecto al hoisting tradicional de variables y funciones. Tanto `let` como `const` son "elevadas" (hoisted), pero no son accesibles a causa de la [Temporal Dead Zone - ENG](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/)
* `let` y `const` están en el ámbito del bloque más cercano que las contiene.
* Cuando uses `const`, usa LETRAS_MAYÚSCULAS.
* A `const` hay que darle un valor cuando se declara.

<br>

### 2. Funciones flecha

Las funciones flecha son un atajo para escribir funciones en ES6. La definición de una función flecha consiste en una lista de parámetros `( ... )`, seguido del símbolo `=>` y seguido el cuerpo de la función.

```javascript
// Forma clásica
let suma = function(a, b) {
    return a + b;
};

// Implementación con funcion flecha
let suma = (a, b) => a + b;
```
Date cuenta que en el ejemplo anterior, la función flecha `suma` esta implementada con una notación concisa en el cuerpo de la función, donde no necesitamos llave ({}) ni una declaración explícita con _return_.

Aquí tenemos un ejemplo con el cuerpo habitual de una función:
```javascript
let arr = ['manzana', 'banana', 'naranja'];

let desayuno = arr.map(fruta => {
    return fruta + 's';
});

console.log(desayuno); // ['manzana', 'banana', 'naranja']
```

**¡Espera! Todavía hay más...**

Las funciones flecha no solo hacen nuestro código más corto. Están muy relacionadas con el comportamiento de `this`.

El comportamiento de la palabra clave `this` varía con respecto a las funciones normales. Cada función en Javascript define su propio contexto, al que hace referencia `this`, pero las funciones flecha capturan el valor de `this` del contexto que las contienen. Fíjate en el siguiente código:


```javascript
function Persona() {
    // El constructor Persona() define `this` como una instancia de sí mismo
    this.edad = 0;

    setInterval(function sumaEdad() {
        // En modo no estricto, la función sumaEdad() define `this`
        // como el objeto global, que es difente del `this` definido
        // en el constructor de Persona()
        this.edad++;
    }, 1000);
}
var p = new Persona();
```

En ECMAScript 3/5, este problema se resolvía guardando el valor de `this` en una variable.

```javascript
function Persona() {
    var self = this;
    self.edad = 0;

    setInterval(function sumaEdad() {
        // La variable `self` contiene el valor
        // de `this` esperado
        self.edad++;
    }, 1000);
}
```

Como se ha mencionado arriba, las funciones flecha capturan el valor de `this` del contexto que las contiene, por tanto el siguiente código funciona según lo previsto:

```javascript
function Persona() {
    this.edad = 0;

    setInterval(() => {
        // `this` se refiere correctamente al objeto persona
        this.edad++;
    }, 1000);
}

var p = new Persona();
```
[Lee más acerca del 'this léxico' en funciones flecha - ENG](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

<br>

### 3. Parámetros de funciones por defecto

ES6 te permite asignar valores por defecto a los parámetros en la definición de las funciones. Aquí un ejemplo:

```javascript
let obtenerPrecioFinal = (precio, tasas = 0.7) => precio + precio * tasas;
obtenerPrecioFinal(500); // 850
```

<br>

### 4. Operador de propagación/soporte

El operador `...` se llama operador de propagación (spread) o de soporte (rest), dependiendo de como se use.

Cuando se usa con cualquier iterable, actúa como operador de propagación, expandiendo el iterable en elementos individuales:

```javascript
function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

El otro uso común de `...` es agrupar un conjunto de valores en un array. En este caso se le llama operador de soporte:

```javascript
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

<br>

### 5. Extensión de objetos literales

ES6 allows declaring object literals by providing shorthand syntax for initializing properties from variables and defining function methods. It also enables the ability to have computed property keys in an object literal definition.

```javascript
function getCar(make, model, value) {
    return {
        // with property value shorthand
        // syntax, you can omit the property
        // value if key matches variable
        // name
        make,  // same as make: make
        model, // same as model: model
        value, // same as value: value

        // computed values now work with
        // object literals
        ['make' + make]: true,

        // Method definition shorthand syntax
        // omits `function` keyword & colon
        depreciate() {
            this.value -= 2500;
        }
    };
}

let car = getCar('Kia', 'Sorento', 40000);
console.log(car);
// {
//     make: 'Kia',
//     model:'Sorento',
//     value: 40000,
//     makeKia: true,
//     depreciate: function()
// }
```

<br>

### 6. Literales Octales y Binarios

ES6 has new support for octal and binary literals.
Prependending a number with `0o` or `0O` would convert it into octal value. Have a look at the following code:

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b or 0B for binary
console.log(bValue); // 2
```

<br>

### 7. Desestructuración de Arrays y Objetos

Destructuring helps in avoiding the need for temp variables when dealing with object and arrays.

```javascript
function foo() {
    return [1, 2, 3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

function bar() {
    return {
        x: 4,
        y: 5,
        z: 6
    };
}
let { x: a, y: b, z: c } = bar();
console.log(a, b, c); // 4 5 6
```

<br>

### 8. super en Objetos

ES6 allows to use `super` method in (classless) objects with prototypes. Following is a simple example:

```javascript
var parent = {
    foo() {
        console.log("Hello from the Parent");
    }
}

var child = {
    foo() {
        super.foo();
        console.log("Hello from the Child");
    }
}

Object.setPrototypeOf(child, parent);
child.foo(); // Hello from the Parent
             // Hello from the Child
```

<br>

### 9. Plantillas de Literales y Delimitadores

ES6 introduces an easier way to add interpolation which are evaluated automatically.

* <code>\`${ ... }\`</code> is used for rendering the variables.
* <code>\`</code> Backtick is used as delimiter.

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

<br>

### 10. for...of vs for...in
* `for...of` iterates over iterable objects, such as array.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
    console.log(nickname);
}
// di
// boo
// punkeye
```

* `for...in` iterates over all enumerable properties of an object.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
    console.log(nickname);
}
// 0
// 1
// 2
// size
```

<br>

### 11. Map y WeakMap

ES6 introduces new set of data structures called `Map` and `WeakMap`. Now, we actually use maps in JavaScript all the time. In fact every object can be considered as a `Map`.

An object is made of keys (always strings) and values, whereas in `Map`, any value (both objects and primitive values) may be used as either a key or a value. Have a look at this piece of code:

```javascript
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function() {};

// setting the values
myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, "value associated with keyObj");
myMap.set(keyFunc, "value associated with keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "value associated with 'a string'"
myMap.get(keyObj);       // "value associated with keyObj"
myMap.get(keyFunc);      // "value associated with keyFunc"
```

**WeakMap**

A `WeakMap` is a Map in which the keys are weakly referenced, that doesn’t prevent its keys from being garbage-collected. That means you don't have to worry about memory leaks.

Another thing to note here- in `WeakMap` as opposed to `Map` *every key must be an object*.

A `WeakMap` only has four methods `delete(key)`, `has(key)`, `get(key)` and `set(key, value)`.

```javascript
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key

var o1 = {},
    o2 = function(){},
    o3 = window;

w.set(o1, 37);
w.set(o2, "azerty");
w.set(o3, undefined);

w.get(o3); // undefined, because that is the set value

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
```

<br>

### 12. Set y WeakSet

Set objects are collections of unique values. Duplicate values are ignored, as the collection must have all unique values. The values can be primitive types or object references.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
```

You can iterate over a set by insertion order using either the `forEach` method or the `for...of` loop.

```javascript
mySet.forEach((item) => {
    console.log(item);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
});

for (let value of mySet) {
    console.log(value);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
}
```
Sets also have the `delete()` and `clear()` methods.

**WeakSet**

Similar to `WeakMap`, the `WeakSet` object lets you store weakly held *objects* in a collection. An object in the `WeakSet` occurs only once; it is unique in the WeakSet's collection.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo has not been added to the set

ws.delete(window); // removes window from the set
ws.has(window);    // false, window has been removed
```

<br>

### 13. Clases en ES6

ES6 introduces new class syntax. One thing to note here is that ES6 class is not a new object-oriented inheritance model. They just serve as a syntactical sugar over JavaScript's existing prototype-based inheritance.

One way to look at a class in ES6 is just a new syntax to work with prototypes and contructor functions that we'd use in ES5.

Functions defined using the `static` keyword implement static/class functions on the class.

```javascript
class Task {
    constructor() {
        console.log("task instantiated!");
    }

    showId() {
        console.log(23);
    }

    static loadAll() {
        console.log("Loading all tasks..");
    }
}

console.log(typeof Task); // function
let task = new Task(); // "task instantiated!"
task.showId(); // 23
Task.loadAll(); // "Loading all tasks.."
```

**extends and super in classes**

Consider the following code:

```javascript
class Car {
    constructor() {
        console.log("Creating a new car");
    }
}

class Porsche extends Car {
    constructor() {
        super();
        console.log("Creating Porsche");
    }
}

let c = new Porsche();
// Creating a new car
// Creating Porsche
```

`extends` allow child class to inherit from parent class in ES6. It is important to note that the derived constructor must call super().

Also, you can call parent class's method in child class's methods using `super.parentMethodName()`

[Read more about classes here](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

A few things to keep in mind:

* Class declarations are not hoisted. You first need to declare your class and then access it, otherwise ReferenceError will be thrown.
* There is no need to use `function` keyword when defining functions inside a class definition.

<br>

### 14. Symbol

A symbol is a unique and immutable data type introduced in ES6. The purpose of a symbol is to generate a unique identifier but you can never get any access to that identifier.

Here’s how you create a symbol:

```javascript
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

Note that you cannot use `new` with `Symbol(…)`.

If a symbol is used as a property/key of an object, it’s stored in a special way that the property will not show up in a normal enumeration of the object’s properties.

```javascript
var o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

To retrieve an object’s symbol properties, use `Object.getOwnPropertySymbols(o)`


<br>

### 15. Iteradores

An iterator accesses the items from a collection one at a time, while keeping track of its current position within that sequence. It provides a `next()` method which returns the next item in the sequence. This method returns an object with two properties: done and value.

ES6 has `Symbol.iterator` which specifies the default iterator for an object. Whenever an object needs to be iterated (such as at the beginning of a for..of loop), its @@iterator method is called with no arguments, and the returned iterator is used to obtain the values to be iterated.

Let’s look at an array, which is an iterable, and the iterator it can produce to consume its values:

```javascript
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
```

Note that you can write custom iterators by defining `obj[Symbol.iterator]()` with the object definition.

<br>

### 16. Generadores

Generator functions are a new feature in ES6 that allow a function to generate many values over time by returning an object which can be iterated over to pull values from the function one value at a time.

A generator function returns an **iterable object** when it's called.
It is written using the new `*` syntax as well as the new `yield` keyword introduced in ES6.

```javascript
function *infiniteNumbers() {
    var n = 1;
    while (true) {
        yield n++;
    }
}

var numbers = infiniteNumbers(); // returns an iterable object

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```

Each time yield is called, the yielded value becomes the next value in the sequence.

Also, note that generators compute their yielded values on demand, which allows them to efficiently represent sequences that are expensive to compute, or even infinite sequences.

<br>

### 17. Promesas

ES6 has native support for promises. A promise is an object that is waiting for an asynchronous operation to complete, and when that operation completes, the promise is either fulfilled(resolved) or rejected.

The standard way to create a Promise is by using the `new Promise()` constructor which accepts a handler that is given two functions as parameters. The first handler (typically named `resolve`) is a function to call with the future value when it's ready; and the second handler (typically named `reject`) is a function to call to reject the Promise if it can't resolve the future value.

```javascript
var p = new Promise(function(resolve, reject) {
    if (/* condition */) {
        resolve(/* value */);  // fulfilled successfully
    } else {
        reject(/* reason */);  // error, rejected
    }
});
```

Every Promise has a method named `then` which takes a pair of callbacks. The first callback is called if the promise is resolved, while the second is called if the promise is rejected.

```javascript
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
```

Returning a value from `then` callbacks will pass the value to the next `then` callback.

```javascript
var hello = new Promise(function(resolve, reject) {
    resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

When returning a promise, the resolved value of the promise will get passed to the next callback to effectively chain them together.
This is a simple technique to avoid "callback hell".

```javascript
var p = new Promise(function(resolve, reject) {
    resolve(1);
});

var eventuallyAdd1 = (val) => {
    return new Promise(function(resolve, reject){
        resolve(val + 1);
    });
}

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)) // 3
```
