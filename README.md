Original Repository: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

# clean-code-javascript

## Mục lục
  1. [Introduction](#introduction)
  2. [Biến](#biến)
  3. [Functions](#functions)
  4. [Đối tượng và Cấu trúc dữ liệu](#đối-tượng-và-cấu-trúc-dữ-liệu)
  5. [Classes](#classes)
  6. [Testing](#testing)
  7. [Xử lí đồng thời](#xử-lí-đồng-thời)
  8. [Xử lí lỗi](#xử-lí-lỗi)
  9. [Định dạng](#định-dạng)
  10. [Viết chú thích](#viết-chú-thích)
  11. [Các ngôn ngữ khác](#các-ngôn-ngữ-khác)

## Introduction
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Software engineering principles, from Robert C. Martin's book
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adapted for JavaScript. This is not a style guide. It's a guide to producing
readable, reusable, and refactorable software in JavaScript.

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over many years of collective experience by the authors of
*Clean Code*.

Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
JavaScript code that you and your team produce.

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

## **Biến**
### Sử dụng tên biến có nghĩa và dễ phát âm

**Không tốt:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Tốt:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ về đầu trang](#mục-lục)**

### Sử dụng cùng từ vựng cho cùng loại biến

**Không tốt:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Tốt:**
```javascript
getUser();
```
**[⬆ về đầu trang](#mục-lục)**

### Sử dụng các tên có thể tìm kiếm được
Chúng ta sẽ đọc code nhiều hơn là viết chúng. Điều quan trọng là code chúng ta
viết có thể đọc được và tìm kiếm được. Việc đặt tên các biến *không* có ngữ
nghĩa so với chương trình, chúng ta có thể sẽ làm người đọc code bị tổn thương
tinh thần.
Hãy làm cho các tên biến của bạn có thể tìm kiếm được. Các công cụ như
[buddy.js](https://github.com/danielstjules/buddy.js) và
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
có thể giúp nhận ra các hằng chưa được đặt tên.

**Không tốt:**
```javascript
// 86400000 là cái quái gì thế?
setTimeout(blastOff, 86400000);

```

**Tốt:**
```javascript
// Khai báo chúng như một biến global.
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```
**[⬆ về đầu trang](#mục-lục)**

### Sử dụng những biến có thể giải thích được
**Không tốt:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Tốt:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```
**[⬆ về đầu trang](#mục-lục)**

### Tránh hại não người khác
Tường minh thì tốt hơn là ẩn.

**Không tốt:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Khoan, `l` làm cái gì vậy?
  dispatch(l);
});
```

**Tốt:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ về đầu trang](#mục-lục)**

### Đừng thêm những ngữ cảnh không cần thiết
Nếu tên của lớp hay đối tượng của bạn đã nói lên điều gì đó rồi, đừng lặp
lại điều đó trong tên biến nữa.

**Không tốt:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Tốt:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ về đầu trang](#mục-lục)**

### Sử dụng những tham số mặc định thay vì kiểm tra các điều kiện lòng vòng

**Không tốt:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Tốt:**
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}

```
**[⬆ về đầu trang](#mục-lục)**

## **Functions**
### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it
makes testing your function easier. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your function is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument.

Since JavaScript allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments.

To make it obvious what properties the function expects, you can use the es6
destructuring syntax. This has a few advantages:

1. When someone looks at the function signature, it's immediately clear what
properties are being used.
2. Destructuring also clones the specified primitive values of the argument
object passed into the function. This can help prevent side effects. Note:
objects and arrays that are destructured from the argument object are NOT
cloned.
3. Linters can warn you about unused properties, which would be impossible
without destructuring.

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```
**[⬆ back to top](#mục-lục)**


### Functions should do one thing
This is by far the most important rule in software engineering. When functions
do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a function to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.

**Bad:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ back to top](#mục-lục)**

### Function names should say what they do

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to to tell from the function name what is added
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ back to top](#mục-lục)**

### Functions should only be one level of abstraction
When you have more than one level of abstraction your function is usually
doing too much. Splitting up functions leads to reusability and easier
testing.

**Bad:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Good:**
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```
**[⬆ back to top](#mục-lục)**

### Remove duplicate code
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one function/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the *Classes* section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**
```javascript
function showList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```
**[⬆ back to top](#mục-lục)**

### Set default objects with Object.assign

**Bad:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ back to top](#mục-lục)**


### Don't use flags as function parameters
Flags tell your user that this function does more than one thing. Functions should do one thing. Split out your functions if they are following different code paths based on a boolean.

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ back to top](#mục-lục)**

### Avoid Side Effects (part 1)
A function produces a side effect if it does anything other than take a value in
and return another value or values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several functions and classes
that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**Bad:**
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ back to top](#mục-lục)**

### Avoid Side Effects (part 2)
In JavaScript, primitives are passed by value and objects/arrays are passed by
reference. In the case of objects and arrays, if our function makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other function that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` function that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` function has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase function
will send the accidentally added item because it has a reference to a shopping
cart array that the `addItemToCart` function modified by adding an unwanted
item.

A great solution would be for the `addItemToCart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other functions that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those case
are pretty rare. Most things can be refactored to have no side effects!

  2. Cloning big objects can be very expensive in terms of performance. Luckily,
this isn't a big issue in practice because there are
[great libraries](https://facebook.github.io/immutable-js/) that allow
this kind of programming approach to be fast and not as memory intensive as
it would be for you to manually clone objects and arrays.

**Bad:**
```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date : Date.now() }];
};
```

**[⬆ back to top](#mục-lục)**

### Don't write to global functions
Polluting globals is a bad practice in JavaScript because you could clash with another
library and the user of your API would be none-the-wiser until they get an
exception in production. Let's think about an example: what if you wanted to
extend JavaScript's native Array method to have a `diff` method that could
show the difference between two arrays? You could write your new function
to the `Array.prototype`, but it could clash with another library that tried
to do the same thing. What if that other library was just using `diff` to find
the difference between the first and last elements of an array? This is why it
would be much better to just use ES2015/ES6 classes and simply extend the `Array` global.

**Bad:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```
**[⬆ back to top](#mục-lục)**

### Favor functional programming over imperative programming
JavaScript isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages are cleaner and easier to test.
Favor this style of programming when you can.

**Bad:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```
**[⬆ back to top](#mục-lục)**

### Encapsulate conditionals

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ back to top](#mục-lục)**

### Avoid negative conditionals

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ back to top](#mục-lục)**

### Avoid conditionals
This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a function should only do
one thing. When you have classes and functions that have `if` statements, you
are telling your user that your function does more than one thing. Remember,
just do one thing.

**Bad:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ back to top](#mục-lục)**

### Avoid type-checking (part 1)
JavaScript is untyped, which means your functions can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your functions. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**Bad:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ back to top](#mục-lục)**

### Avoid type-checking (part 2)
If you are working with basic primitive values like strings, integers, and arrays,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using TypeScript. It is an excellent alternative to normal
JavaScript, as it provides you with static typing on top of standard JavaScript
syntax. The problem with manually type-checking normal JavaScript is that
doing it well requires so much extra verbiage that the faux "type-safety" you get
doesn't make up for the lost readability. Keep your JavaScript clean, write
good tests, and have good code reviews. Otherwise, do all of that but with
TypeScript (which, like I said, is a great alternative!).

**Bad:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ back to top](#mục-lục)**

### Don't over-optimize
Modern browsers do a lot of optimization under-the-hood at runtime. A lot of
times, if you are optimizing then you are just wasting your time. [There are good
resources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
for seeing where optimization is lacking. Target those in the meantime, until
they are fixed if they can be.

**Bad:**
```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ back to top](#mục-lục)**

### Remove dead code
Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Bad:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ back to top](#mục-lục)**

## **Đối tượng và Cấu trúc dữ liệu**
### Sử dụng getter và setter
JavaScript không có interface hoặc kiểu vì vậy rất khó để thực hiện mô hình này,
bởi vì chúng ta không có các từ khoá như `public` và `private`. Vì vậy, sử dụng
getters và setters để truy cập dữ liệu trên các đối tượng thì tốt hơn là chỉ đơn
giản tìm kiếm một thuộc tính trên một đối tượng. Bạn có thể hỏi "Tại sao?".
Đây là một danh sách các lí do tại sao:

* Khi bạn muốn thực hiện nhiều hơn việc lấy một thuộc tính của đối tượng, bạn không
cần phải tìm kiếm và thay đổi mỗi accessor trong codebase của bạn.
* Làm cho việc thêm các validation đơn giản khi thực hiện trên một `tập hợp`.
* Đóng gói các biểu diễn nội bộ.
* Dễ dàng thêm log và xử lí lỗi khi getting và setting.
* Kế thừa lớp này, bạn có thể override những hàm mặc định.
* Bạn có thể lazy load các thuộc tính của một đối tượng, lấy nó từ server.


**Không tốt:**
```javascript
class BankAccount {
  constructor() {
    this.balance = 1000;
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= 100;
```

**Tốt:**
```javascript
class BankAccount {
  constructor(balance = 1000) {
    this._balance = balance;
  }

  // Không cần phải thêm tiền tố `get` hay `set` để trở thành một getter hay setter
  set balance(amount) {
    if (this.verifyIfAmountCanBeSetted(amount)) {
      this._balance = amount;
    }
  }

  get balance() {
    return this._balance;
  }

  verifyIfAmountCanBeSetted(val) {
    // ...
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= shoesPrice;

// Get balance
let balance = bankAccount.balance;

```
**[⬆ về đầu trang](#mục-lục)**


### Làm cho các đối tượng có thành viên private
Điều này có thể được thực hiện thông qua closures (cho ES5 và cũ hơn).

**Không tốt:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Tốt:**
```javascript
const Employee = function (name) {
  this.getName = function getName() {
    return name;
  };
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ về đầu trang](#mục-lục)**


## **Classes**
### Single Responsibility Principle (SRP)
As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify a piece of it,
it can be difficult to understand how that will affect other dependent modules in
your codebase.

**Bad:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ back to top](#mục-lục)**

### Open/Closed Principle (OCP)
As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```
**[⬆ back to top](#mục-lục)**


### Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Will return 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor() {
    super();
    this.width = 0;
    this.height = 0;
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor() {
    super();
    this.length = 0;
  }

  setLength(length) {
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    switch (shape.constructor.name) {
      case 'Square':
        shape.setLength(5);
        break;
      case 'Rectangle':
        shape.setWidth(4);
        shape.setHeight(5);
    }

    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeShapes(shapes);
```
**[⬆ back to top](#mục-lục)**

### Interface Segregation Principle (ISP)
JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a "fat interface".

**Bad:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});

```

**Good:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ back to top](#mục-lục)**

### Dependency Inversion Principle (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

This can be hard to understand at first, but if you've worked with Angular.js,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Bad:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Good:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ back to top](#mục-lục)**

### Prefer ES2015/ES6 classes over ES5 plain functions
It's very difficult to get readable class inheritance, construction, and method
definitions for classical ES5 classes. If you need inheritance (and be aware
that you might not), then prefer classes. However, prefer small functions over
classes until you find yourself needing larger and more complex objects.

**Bad:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ back to top](#mục-lục)**


### Use method chaining
This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Good:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ back to top](#mục-lục)**

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Bad:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ back to top](#mục-lục)**

## **Testing**
Testing thì quan trọng hơn shipping. Nếu bạn không có test hoặc không đủ,
thì mỗi lần ship code bạn sẽ không chắc là mình có làm hư hại thứ gì không.
Việc quyết định những gì để tạo thành số lượng test đủ là do team của bạn,
nhưng việc có 100% độ bao phủ (tất cả các câu lệnh và rẽ nhánh) là cách để
bạn đạt được sự tự tin cao. Điều này có nghĩa ngoài việc có được một framework
để test tốt, bạn cũng cần sử dụng một [công cụ bao phủ tốt](http://gotwarlost.github.io/istanbul/).

Không có lí do gì để không viết test. Có [rất nhiều framework test JS tốt](http://jstherightway.org/#testing-tools),
vì thế hãy tìm một framework mà team bạn thích. Khi đã tìm được một cái thích
hợp với team của mình, hãy đặt mục tiêu để luôn luôn viết test cho mỗi tính
năng hoặc module mới của bạn. Nếu phương pháp test ưa thích của bạn là Test
Driven Development (TDD), điều đó thật tuyệt, nhưng điểm quan trọng là phải
chắc chắn bạn đạt được mục tiêu về độ bao phủ trước khi launch một tính năng
hoặc refactor một tính năng cũ nào đó.

### Một khái niệm duy nhất cho mỗi đơn vị test

**Không tốt:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Tốt:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ về đầu trang](#mục-lục)**

## **Xử lí đồng thời**
### Hãy dùng Promise, đừng dùng callback
Callback thì không được 'sạch sẽ' cho lắm, chúng gây ra quá nhiều đoạn code lồng nhau
(callback hell). Từ ES2015/ES6, Promise đã được đưa vào Javascript. Hãy sử dụng chúng!

**Không tốt:**
```javascript
require('request').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    require('fs').writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Tốt:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```
**[⬆ về đầu trang](#mục-lục)**

### Async/Await thì 'sạch sẽ' hơn Promise
Promise là một sự thay thế 'sạch sẽ' cho callback, nhưng ES2017/ES8 giới thiệu
async và await, đó thậm chí còn là một giải pháp tốt hơn Promise nữa. Những gì
bạn cần phải làm là một hàm có tiếp đầu ngữ là từ khoá `async`, và bạn có thể viết
các lệnh logic mà không cần một chuỗi `then` của các hàm. Hãy sử dụng điều này nếu
bạn có thể tận dụng các tính năng của ES2017/ES8 ngay hôm nay!

**Không tốt:**
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Tốt:**
```javascript
async function getCleanCodeArticle() {
  try {
    const response = await require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await require('fs-promise').writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ về đầu trang](#mục-lục)**


## **Xử lí lỗi**
Thông báo lỗi là một điều tốt! Nghĩa là chương trình của bạn nhận dạng
được khi có một cái gì đó chạy không đúng và nó sẽ cho bạn biết bằng việc
dừng chức năng mà nó đang thực thi, huỷ tiến trình (trong Node), và thông
báo cho bạn trong console với một stack để theo dấu.

### Đừng bỏ qua những lỗi đã bắt được
Nếu không làm gì với lỗi đã bắt được, bạn sẽ không thể sửa hoặc phản ứng
lại được với lỗi đó. Ghi lỗi ra console (`console.log`) cũng không tốt hơn
bao nhiêu vì đa số nó có thể bị trôi mất trong một đống những thứ được hiển
thị ra ở console. Nếu bạn đặt bất cứ đoạn code nào trong một block `try/catch`,
tức là bạn nghĩ một lỗi có thể xảy ra ở đây, do đó bạn nên có một giải pháp
hoặc tạo một luồng code để xử lí lỗi khi nó xảy ra.

**Không tốt:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Tốt:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Đừng bỏ qua những promise bị lỗi (rejected)
Cùng nguyên nhân với phần trên.

**Không tốt:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});
```

**Tốt:**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
});
```

**[⬆ về trang chủ](#mục-lục)**


## **Định dạng**
Việc định dạng code mang tính chủ quan. Giống như nhiều quy tắc được trình
bày trong tài liệu này, không có quy tắc nào cứng nhắc và nhanh chóng mà bạn
bắt buộc phải tuân theo. Điểm chính của phần này là ĐỪNG BAO GIỜ TRANH CÃI
về việc định dạng code như thế nào. Có [hàng tá công cụ](http://standardjs.com/rules.html)
để tự động hoá việc này. Hãy sử dụng một công cụ nào đó! Thật tốn thời gian và
tiền bạc chỉ để tranh cãi về vấn đề định dạng code.

Đối với những thứ không thuộc phạm vi của việc tự động định dạng code (thụt đầu
dòng, tab và space, nháy đơn và nháy kép,..) hãy xem một số hướng dẫn ở đây.

### Sử dụng thống nhất cách viết hoa
Javascript là một ngôn ngữ không định kiểu, vì vậy việc viết hoa sẽ nói lên rất
nhiều về các biến, hàm,.. của bạn. Những quy tắc này thì mang tính chủ quan,
vì thế team bạn có thể chọn quy tắc nào họ muốn. Tuy nhiên điều quan trọng là
dù bạn chọn cách viết như thế nào, thì cũng hãy sử dụng thống nhất nó trong
codebase của bạn.

**Không tốt:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Tốt:**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ về đầu trang](#mục-lục)**


### Các hàm gọi và hàm được gọi nên nằm gần nhau
Nếu một hàm gọi một hàm  khác, hãy giữ những hàm này nằm gần theo chiều dọc trong
file. Lí tưởng là, hãy giữ cho hàm gọi ở trên hàm được gọi. Chúng ta có xu hướng
đọc code từ trên xuống, giống như đọc báo vậy. Do đó, hãy làm cho code của chúng
ta cũng được đọc theo cách đó.

**Không tốt:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Tốt:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ về đầu trang](#mục-lục)**

## **Viết chú thích**
### Chỉ nên viết chú thích cho những thứ có logic phức tạp.
Các chú thích thường là lời xin lỗi, chứ không phải là yêu cầu.
Những đoạn code tốt thì *đa số* tự nó đã là tài liệu rồi.

**Không tốt:**
```javascript
function hashIt(data) {
  // Khai báo hash
  let hash = 0;

  // Lấy chiều dài của chuỗi
  const length = data.length;

  // Lặp qua mỗi kí tự
  for (let i = 0; i < length; i++) {
    // Lấy mã của kí tự
    const char = data.charCodeAt(i);
    // Gán giá trị cho hash
    hash = ((hash << 5) - hash) + char;
    // Chuyển thành định dạng số nguyên 32 bit
    hash &= hash;
  }
}
```

**Tốt:**
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Chuyển thành định dạng số nguyên 32 bit
    hash &= hash;
  }
}

```
**[⬆ về đầu trang](#mục-lục)**

### Đừng giữ lại những đoạn code bị chú thích trong codebase của bạn.
Những công cụ quản lí phiên bản sinh ra để làm nhiệm vụ của chúng.
Hãy để code cũ của bạn nằm lại trong dĩ vãng đi.

**Không tốt:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Tốt:**
```javascript
doStuff();
```
**[⬆ về đầu trang](#mục-lục)**

### Đừng viết các chú thích nhật ký.
Hãy nhớ, sử dụng công cụ quản lí phiên bản! Chúng ta không cần những đoạn code
vô dụng, bị chú thích và đặc biệt là những chú thích dạng nhật ký...
Sử dụng `git log` để xem lịch sử được mà!

**Không tốt:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Tốt:**
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ về đầu trang](#mục-lục)**

### Tránh những đánh dấu vị trí
Chúng thường xuyên làm nhiễu code. Hãy để những tên hàm, biến cùng với các
định dạng thích hợp tự tạo thành cấu trúc trực quan cho code của bạn.

**Không tốt:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Tốt:**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
**[⬆ về đầu trang](#mục-lục)**

## Các ngôn ngữ khác

Tài liệu này cũng có sẵn ở các ngôn ngữ sau:

  - ![en](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags-iso/shiny/24/US.png) **English**: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)
  - ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese**: [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)

**[⬆ về đầu trang](#mục-lục)**
