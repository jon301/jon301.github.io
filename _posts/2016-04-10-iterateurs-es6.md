---
layout: post
title: "Itérateurs ES6 / ES2015"
date: "2016-04-10 12:54"
categories:
    - javascript
tags:
    - es6
comments: true
---

### Instruction `for...of`

ES6 introduit un nouveau type de boucle `for...of` permettant de parcourir et
récupérer plus facilement les valeurs d'éléments itérables.

```javascript
var arr = ['a', 'b', 'c'];

// ES5
for (var i = 0; i < arr.length; ++i) {
    console.log(arr[i]); // a b c
}

// ES6
for (let value of arr) {
    console.log(value); // a b c
}
```

Cette instruction ne peut fonctionner qu'avec des objets implémentant l'interface
[Iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable){:target="_blank"}.

Certains objets standards tels que
[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array){:target="_blank"},
[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map){:target="_blank"},
[Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set){:target="_blank"},
[String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String){:target="_blank"} ou
[TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray){:target="_blank"}
supportent nativement cette interface et peuvent ainsi être directement utilisés avec l'instruction `for...of`.

### Interface Iterable

Pour qu'un objet soit itérable, il doit tout simplement implémenter une méthode `[Symbol.iterator]()`.

Cette méthode doit retourner un objet ayant une méthode `.next()` retournant elle-même un objet comportant ces 2 propriétés:

- `value`: la prochaine valeur de l'itération
- `done`: un booléen indiquant la fin de l'itération

### Exemple

Codons un simple objet `counter` implémentant l'interface [Iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable){:target="_blank"}.
Ce compteur aura pour but de retourner un nombre entier s'incrémentant à chaque
itération jusqu'à un maximum de 5:

```javascript
var counter = {
    [Symbol.iterator]() {
        let i = 0;
        return {
            next() {
                return i <= 5 ?
                { value: i++, done: false } :
                { done: true }
            }
        }
    }
}

// Il est maintenant possible d'itérer sur cet objet avec l'instruction for...of
for (let val of counter) {
    console.log(val); // 0 1 2 3 4 5
}
```

On peut également boucler manuellement sur un itérateur. En reprenant l'exemple
`counter` précédent:

```javascript
// Récupération de l'itérateur de l'objet counter
let it = counter[Symbol.iterator]();

let el = it.next();
console.log(el.value, el.done); // 0 false

el = it.next();
console.log(el.value, el.done); // 1 false

el = it.next();
console.log(el.value, el.done); // 2 false

el = it.next();
console.log(el.value, el.done); // 3 false

el = it.next();
console.log(el.value, el.done); // 4 false

el = it.next();
console.log(el.value, el.done); // 5 false

el = it.next();
console.log(el.value, el.done); // undefined true
```

### Références
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of){:target="_blank"}
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols){:target="_blank"}

