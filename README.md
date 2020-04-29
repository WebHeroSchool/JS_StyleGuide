# Подборка правил по стилю JavaScript


### **Используйте `const` для объявления переменных; избегайте var.**

>Почему? Это гарантирует, что вы не сможете 
переопределять значения, т.к. это может привести 
к ошибкам и к усложнению понимания кода.

		// bad
		var a = 1;
		var b = 2;

		// good
		const a = 1;
		const b = 2;

### **Не вызывайте напрямую методы `Object.prototype`, такие как `hasOwnProperty`, `propertyIsEnumerable`, и `isPrototypeOf`.** 

>Почему? Эти методы могут быть переопределены в свойствах объекта, который мы проверяем { hasOwnProperty: false }, или этот объект может быть null (Object.create(null)).

     // bad
    console.log(object.hasOwnProperty(key));

	 // good
	console.log(Object.prototype.hasOwnProperty.call(object, key));

	// excellent
	const has = Object.prototype.hasOwnProperty; // Кэшируем запрос в рамках 	модуля.
	console.log(has.call(object, key));
	/* или */
	import has from 'has'; // https://www.npmjs.com/package/has
	console.log(has(object, key));

### **Для копирования массивов используйте оператор расширения `...`.**

    // bad
    const len = items.length;
    const itemsCopy = [];
    let i;

    for (i = 0; i < len; i += 1) {
      itemsCopy[i] = items[i];
    }

    // good
    const itemsCopy = [...items];

### **Используйте операторы `return` внутри функций обратного вызова в методах массива. Можно опустить `return`, когда тело функции состоит из одной инструкции, возвращающей выражение без побочных эффектов.**

    // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });

    // good
    [1, 2, 3].map((x) => x + 1);

    // плохо - нет возвращаемого значения, следовательно, `acc` становится 
    `undefined` после первой итерации
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
      const flatten = acc.concat(item);
    });

    // good
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
      const flatten = acc.concat(item);
      return flatten;
    });

    // bad
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      } else {
        return false;
      }
    });

    // good
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      }

      return false;
    });

### **При обращении к нескольким свойствам объекта используйте деструктуризацию объекта.**

>Почему? Деструктуризация избавляет вас от создания временных переменных для этих свойств.

            // bad
		function getFullName(user) {
		  const firstName = user.firstName;
		  const lastName = user.lastName;

		  return `${firstName} ${lastName}`;
		}

		// good
		function getFullName(user) {
		  const { firstName, lastName } = user;
		  return `${firstName} ${lastName}`;
		}

		// excellent
		function getFullName({ firstName, lastName }) {
		  return `${firstName} ${lastName}`;
		}

### **Используйте одинарные кавычки `''` для строк.**

    // bad
	const name = "Capt. Janeway";

	// bad - литерал шаблонной строки должен содержать интерполяцию или переводы строк
	const name = `Capt. Janeway`;

	// good
	const name = 'Capt. Janeway';


### **Никогда не называйте параметр `arguments`. Он будет иметь приоритет над объектом `arguments`, который доступен для каждой функции.**

    // bad
	function foo(name, options, arguments) {
	  // ...
	}

	// good
	function foo(name, options, args) {
	  // ...
	}

### **Когда вам необходимо использовать анонимную функцию (например, при передаче встроенной функции обратного вызова), используйте стрелочную функцию.**

>Почему? Таким образом создаётся функция, которая выполняется в контексте `this`, который мы обычно хотим, а также это более короткий синтаксис.

>Почему бы и нет? Если у вас есть довольно сложная функция, вы можете переместить эту логику внутрь её собственного именованного функционального выражения.

    // bad
	[1, 2, 3].map(function (x) {
	  const y = x + 1;
	  return x * y;
	});

	// good
	[1, 2, 3].map((x) => {
	  const y = x + 1;
	  return x * y;
	});

### **Всегда используйте `class`. Избегайте прямых манипуляций с `prototype`.**

>Почему? Синтаксис `class` является кратким и понятным.

    // bad
	function Queue(contents = []) {
	  this.queue = [...contents];
	}
	Queue.prototype.pop = function () {
	  const value = this.queue[0];
	  this.queue.splice(0, 1);
	  return value;
	};

	// good
	class Queue {
	  constructor(contents = []) {
	    this.queue = [...contents];
	  }
	  pop() {
	    const value = this.queue[0];
	    this.queue.splice(0, 1);
	    return value;
	  }
	}

### **Используйте `extends` для наследования.**

>Почему? Это встроенный способ наследовать функциональность прототипа, не нарушая `instanceof`.

    // bad
	const inherits = require('inherits');
	function PeekableQueue(contents) {
	  Queue.apply(this, contents);
	}
	inherits(PeekableQueue, Queue);
	PeekableQueue.prototype.peek = function () {
	  return this.queue[0];
	};

	// good
	class PeekableQueue extends Queue {
	  peek() {
	    return this.queue[0];
	  }
	}

