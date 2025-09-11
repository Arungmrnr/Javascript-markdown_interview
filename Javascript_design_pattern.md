# JavaScript Design Patterns (Browser + Node.js)

Design patterns are **reusable solutions** to recurring problems in software development.  
They are **not strict code**, but **guidelines/templates** for structuring solutions in a clean and maintainable way.  

---

## 📚 Why Use Design Patterns?
- Provide a **shared vocabulary** between developers.  
- Promote **best practices** and **consistency**.  
- Avoid reinventing the wheel.  
- Manage **complexity** in large-scale applications.  

---

## 🗂️ Categories of Patterns
1. **Creational** → How to create objects.  
2. **Structural** → How to organize and compose objects.  
3. **Behavioral** → How objects interact and communicate.  

👉 Easy way to remember: **Born → Build → Behave** (Create → Structure → Behave).  

---

# 🌱 Creational Patterns

## 1. Singleton
**Ensure only one instance exists** across the application.  

```js
// Node.js example: Database connection
class DB {
  constructor() {
    if (DB.instance) return DB.instance; // reuse if already exists
    this.connection = "connected";
    DB.instance = this;
  }
}

const db1 = new DB();
const db2 = new DB();
console.log(db1 === db2); // true
```

🔑 **Interview recap**:  
- Problem: Avoid multiple instances (e.g., DB connections).  
- Browser: `window` object.  
- Node.js: `require()` caches modules.  
- Hook: 🎟️ “Only one ticket per person.”  

---

## 2. Factory
**Encapsulate object creation** without `new` everywhere.  

```js
function userFactory(type) {
  switch (type) {
    case "admin": return { role: "admin", canDelete: true };
    case "guest": return { role: "guest", canDelete: false };
    default: return { role: "user", canDelete: false };
  }
}

const admin = userFactory("admin");
console.log(admin);
```

🔑 **Interview recap**:  
- Problem: Centralized object creation.  
- Browser: `document.createElement("div")`.  
- Node.js: DB driver factories.  
- Hook: 🏭 “Factory makes products on demand.”  

---

## 3. Prototype
**Share behavior via prototypes instead of duplicating.**

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHi = function() {
  console.log("Hi, I'm " + this.name);
};

const user = new Person("Alice");
user.sayHi(); // Hi, I'm Alice
```

🔑 **Interview recap**:  
- Problem: Memory-efficient inheritance.  
- Browser: DOM nodes inherit from `Node.prototype`.  
- Node.js: `util.inherits()`.  
- Hook: 📋 “Blueprint for clones.”  

---

# 🏗️ Structural Patterns

## 1. Module
**Encapsulation**: Expose only what you want.  

```js
// Browser IIFE
const counter = (function() {
  let count = 0; // private
  return {
    inc: () => ++count,
    get: () => count
  };
})();

console.log(counter.inc()); // 1
console.log(counter.get()); // 1
```

🔑 **Interview recap**:  
- Problem: Organize code, avoid globals.  
- Browser: ES Modules, IIFE.  
- Node.js: `module.exports`.  
- Hook: 🥡 “Box with a lid.”  

---

## 2. Decorator
**Add new features without changing the original.**

```js
function withTimestamp(fn) {
  return function(...args) {
    console.log("Time:", Date.now());
    return fn(...args);
  };
}

function sayHi(name) { console.log("Hi", name); }

const loggedHi = withTimestamp(sayHi);
loggedHi("Arun");
```

🔑 **Interview recap**:  
- Problem: Extend objects/functions dynamically.  
- Browser: React HOCs.  
- Node.js: Express middleware.  
- Hook: 🍕 “Extra toppings on pizza.”  

---

## 3. Proxy
**Control access or behavior** of an object.  

```js
const user = { name: "Alice", age: 20 };

const proxyUser = new Proxy(user, {
  get(target, prop) {
    if (prop === "age") return target[prop] + " years";
    return target[prop];
  }
});

console.log(proxyUser.age); // "20 years"
```

🔑 **Interview recap**:  
- Problem: Intercept and control operations.  
- Browser: Vue reactivity system.  
- Node.js: API gateways, caching layers.  
- Hook: 🛡️ “Bodyguard at the door.”  

---

## 4. Facade
**Simplify a complex system** with one interface.  

```js
// Browser DOM example
document.querySelector("#app"); 
// Behind the scenes, replaces multiple getElement calls
```

🔑 **Interview recap**:  
- Problem: Hide complexity.  
- Browser: `querySelector`.  
- Node.js: ORMs like Mongoose.  
- Hook: 🛎️ “Receptionist simplifies everything.”  

---

# 🎭 Behavioral Patterns

## 1. Observer
**Publish/subscribe** communication.  

```js
class Observable {
  constructor() { this.subs = []; }
  subscribe(fn) { this.subs.push(fn); }
  notify(data) { this.subs.forEach(fn => fn(data)); }
}

const news = new Observable();
news.subscribe(d => console.log("Reader A:", d));
news.subscribe(d => console.log("Reader B:", d));

news.notify("Breaking news!");
```

🔑 **Interview recap**:  
- Problem: Decouple publisher and subscribers.  
- Browser: DOM events.  
- Node.js: `EventEmitter`.  
- Hook: 📰 “Newspaper delivery.”  

---

## 2. Strategy
**Choose algorithm at runtime.**

```js
function paymentStrategy(strategy) {
  return strategy.pay();
}

class PayPal { pay() { console.log("Paying with PayPal"); } }
class Stripe { pay() { console.log("Paying with Stripe"); } }

paymentStrategy(new PayPal());
paymentStrategy(new Stripe());
```

🔑 **Interview recap**:  
- Problem: Swap different algorithms easily.  
- Browser: Different form validation rules.  
- Node.js: Payment gateways.  
- Hook: 🗺️ “Different routes, same destination.”  

---

## 3. Command
**Encapsulate actions as objects.**

```js
class Command {
  constructor(execute, undo) {
    this.execute = execute;
    this.undo = undo;
  }
}

const addCommand = new Command(
  (x, y) => x + y,
  (res, y) => res - y
);

console.log(addCommand.execute(2, 3)); // 5
console.log(addCommand.undo(5, 3));    // 2
```

🔑 **Interview recap**:  
- Problem: Encapsulate request/undo actions.  
- Browser: Undo/redo system.  
- Node.js: CLI tools.  
- Hook: 🎛️ “Remote control buttons.”  

---

## 4. Iterator
**Step through a collection one by one.**

```js
const collection = [1, 2, 3];
const iterator = collection[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
```

🔑 **Interview recap**:  
- Problem: Sequential access.  
- Browser: `for...of` iteration.  
- Node.js: Streams.  
- Hook: 🚶 “Tour guide step by step.”  

---

## 5. Mediator
**Central hub for communication.**

```js
class ChatRoom {
  showMessage(user, message) {
    console.log(`${user}: ${message}`);
  }
}

const chat = new ChatRoom();
chat.showMessage("Alice", "Hello!");
chat.showMessage("Bob", "Hi Alice!");
```

🔑 **Interview recap**:  
- Problem: Reduce direct coupling.  
- Browser: Chat mediator.  
- Node.js: Message brokers (Redis, RabbitMQ).  
- Hook: 🛫 “Air traffic control tower.”  

---

# 📝 Quick Summary Table

| Category   | Pattern    | Problem it Solves                 | Browser Example            | Node.js Example             | Memory Hook |
|------------|-----------|-----------------------------------|----------------------------|-----------------------------|-------------|
| Creational | Singleton | One instance only                 | `window`                   | `require()` cache           | 🎟️ seat |
| Creational | Factory   | Object creation abstraction       | `document.createElement`   | DB factory                  | 🏭 factory |
| Creational | Prototype | Inheritance via prototypes        | DOM prototypes             | `util.inherits()`           | 📋 blueprint |
| Structural | Module    | Encapsulation                     | IIFE/ES Modules            | `module.exports`            | 🥡 box |
| Structural | Decorator | Extend behavior dynamically       | React HOC                  | Express middleware          | 🍕 toppings |
| Structural | Proxy     | Control access                    | Vue reactivity             | API gateway                 | 🛡️ guard |
| Structural | Facade    | Simplify complex systems          | `querySelector`            | Mongoose ORM                | 🛎️ receptionist |
| Behavioral | Observer  | Publish/subscribe                 | DOM events                 | `EventEmitter`              | 📰 newspaper |
| Behavioral | Strategy  | Switch algorithms at runtime      | Form validators            | Payment providers           | 🗺️ route |
| Behavioral | Command   | Encapsulate actions               | Undo/Redo                  | CLI commands                | 🎛️ remote |
| Behavioral | Iterator  | Sequential access                 | `for...of`                 | Streams                     | 🚶 tour |
| Behavioral | Mediator  | Centralized communication         | Chat mediator              | Message broker              | 🛫 tower |

---

# 🚀 Interview Strategy
When asked about design patterns:  

1. Start with **definition**:  
   > “Design patterns are reusable solutions to common design problems. They fall into creational, structural, and behavioral categories.”  

2. Mention **categories**:  
   - Creational → how objects are made.  
   - Structural → how they’re organized.  
   - Behavioral → how they interact.  

3. Give **one real-world JS example** for each.  

4. Close with **practical link**:  
   > “For example, the Observer pattern is just like DOM events in the browser or EventEmitter in Node.js.”  

---
