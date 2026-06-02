# Chapter 10: Object-Oriented Programming (OOP) and Prototypes

Object-Oriented Programming (OOP) is a programming paradigm that organizes software design around data, or "objects," rather than functions and logic. In JavaScript, OOP functions somewhat differently than in languages like Java, C++, or Python. 

While modern JavaScript uses a familiar `class` syntax, under the hood it relies on a system known as **Prototypal Inheritance**. In this chapter, we will look at how prototypes work, how objects were historically constructed using functions, how to write modern ES6 classes, and how to apply concepts like inheritance and encapsulation using native private fields.

---

## 10.1 Understanding Prototypal Inheritance

In class-based languages, a class is a blueprint, and an object is a distinct instance created from that blueprint. In JavaScript, objects are linked directly to other objects.

Every JavaScript object has an internal, hidden property called `[[Prototype]]`. This property points to another object, which acts as a fallback source of properties and methods.

### The Prototype Chain
When you try to access a property or a method on an object, JavaScript follows a specific search path:
1. It looks for the property directly on the object itself.
2. If it is not found, it looks at the object's `[[Prototype]]`.
3. If it is still not found, it continues up the chain of prototypes.
4. The chain ends at `Object.prototype`. If the property is still missing, it returns `undefined`.

```
[ myObject ]
     |
     v (looks up [[Prototype]])
[ parentPrototype ]
     |
     v (looks up [[Prototype]])
[ Object.prototype ]  --->  null (end of chain)
```

We can inspect an object's prototype using `Object.getPrototypeOf()`:

```javascript
const animal = {
    eats: true
};

const rabbit = {
    jumps: true
};

// Historically, __proto__ was used to set prototypes. 
// Today, we use Object.setPrototypeOf() to establish this relationship programmatically.
Object.setPrototypeOf(rabbit, animal);

console.log(rabbit.jumps); // Outputs: true (found on the rabbit object itself)
console.log(rabbit.eats);  // Outputs: true (found on the parent "animal" prototype)
```

---

## 10.2 Constructor Functions and the `new` Keyword

Before the modern class syntax was introduced in 2015, developers simulated classes using **Constructor Functions** combined with the `new` keyword.

```javascript
// By convention, constructor functions start with a Capital letter
function User(username, email) {
    this.username = username;
    this.email = email;
}

const user1 = new User("alice1", "alice@test.com");
```

### What does the `new` keyword do?
When you invoke a function with `new`, JavaScript automatically performs four operations behind the scenes:
1. It creates a brand new, empty object.
2. It binds `this` inside the constructor function to that new object.
3. It links the new object's internal `[[Prototype]]` to the constructor function’s `.prototype` property.
4. It implicitly returns the new object (unless the function explicitly returns another object).

### Efficient Method Storage
If you define methods inside the constructor function, every single object instance you create will get its own copy of that function in memory:

```javascript
function InefficientUser(name) {
    this.name = name;
    this.greet = function() { console.log("Hi!"); }; // Bad: Copied for every instance
}
```

To save system memory, we attach methods to the constructor's `.prototype` property. This way, all instances share a single copy of the method.

```javascript
function EfficientUser(name) {
    this.name = name;
}

// All instances of EfficientUser point to this single method in memory
EfficientUser.prototype.greet = function() {
    console.log(`Hello, my name is ${this.name}`);
};

const u1 = new EfficientUser("Bob");
u1.greet(); // Outputs: "Hello, my name is Bob"
```

---

## 10.3 Modern ES6 Classes

In ES6, JavaScript introduced the `class` keyword. It is important to note that classes in JavaScript are primarily "syntactic sugar"—a cleaner, easier-to-read syntax built on top of the existing prototype system.

Here is how we write the same `EfficientUser` logic using modern class syntax:

```javascript
class User {
    // The constructor is run automatically when 'new User()' is called
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }

    // Methods are automatically added to User.prototype, sharing memory efficiently
    greet() {
        console.log(`Hello, my name is ${this.name}`);
    }
    
    showEmail() {
        console.log(`Email: ${this.email}`);
    }
}

const user2 = new User("Charlie", "charlie@test.com");
user2.greet(); // Outputs: "Hello, my name is Charlie"
```

---

## 10.4 Inheritance with `extends` and `super`

Inheritance allows us to create a new class (a child class or subclass) that inherits properties and methods from an existing class (a parent class or superclass). This is useful for avoiding duplicate code across similar categories of objects.

We use the `extends` keyword to link a child class to a parent class, and the `super` keyword to call the parent's constructor.

```javascript
// Parent Class
class Asset {
    constructor(name, price) {
        this.name = name;
        this.price = price;
    }

    describe() {
        return `${this.name} costs $${this.price}`;
    }
}

// Child Class inherits from Asset
class House extends Asset {
    constructor(name, price, bedrooms) {
        // super() MUST be called before you can use the 'this' keyword in a subclass
        super(name, price); 
        this.bedrooms = bedrooms;
    }

    // Method Overriding: We can replace or extend parent methods
    describe() {
        return `${super.describe()} and has ${this.bedrooms} bedrooms.`;
    }
}

const myHome = new House("Suburban Villa", 350000, 4);
console.log(myHome.describe()); 
// Outputs: "Suburban Villa costs $350000 and has 4 bedrooms."
```

---

## 10.5 Encapsulation using Private Fields (`#`)

**Encapsulation** is the practice of hiding the internal state and implementation details of an object, exposing only a clean, controlled public interface. 

Historically, JavaScript lacked built-in mechanisms to restrict access to properties. Developers used naming conventions, such as prefixing a variable with an underscore (e.g., `this._balance`) to indicate it shouldn't be touched directly.

In modern JavaScript (standardized in ES2022), you can declare truly **private fields** and **private methods** using the hash symbol `#`. Private properties must be declared at the top of the class definition and cannot be accessed from outside the class.

```javascript
class BankAccount {
    // 1. Declare private fields at the top of the class
    #balance;

    constructor(owner, initialDeposit) {
        this.owner = owner;
        this.#balance = initialDeposit; // Initializing private variable
    }

    // Public method to securely read the balance (Getter)
    getBalance() {
        return this.#balance;
    }

    // Public method to securely modify the balance (Setter-like logic)
    deposit(amount) {
        if (amount > 0) {
            this.#balance += amount;
            console.log(`Deposited $${amount}. New balance: $${this.#balance}`);
        } else {
            console.log("Invalid deposit amount.");
        }
    }
}

const account = new BankAccount("David", 500);
account.deposit(200); // Outputs: "Deposited $200. New balance: $700"

// Attempting to read or modify the private property directly results in a Syntax Error
// console.log(account.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```

By hiding `#balance`, we guarantee that the money inside the account can only be modified through the validated `deposit` method, preventing arbitrary values (like negative numbers) from breaking our program logic.

---

## Exercises and Review

Put your object-oriented JavaScript skills into practice with these challenges:

1. **The Prototype Explorer:** Write a constructor function named `Book` that takes `title` and `author` as arguments. Add a method named `getDetails` directly to `Book.prototype` that returns a string format of the title and author. Instantiate two book objects and call the method.
2. **The Vehicle Hierarchy:** Create a parent class named `Vehicle` with properties `make` and `model`. Add a subclass named `ElectricVehicle` that extends `Vehicle` and includes an additional property `batteryRange` (number). Ensure both classes utilize constructors correctly.
3. **The Secure Smart Lock:** Design a class named `SmartLock`.
   * Include a private field named `#passcode`.
   * Set the passcode inside the constructor.
   * Create a public method `unlock(enteredPasscode)` that prints `"Access Granted"` if the passcode matches the private field, and `"Access Denied"` if it does not. Try to access the `#passcode` property directly on your instance to verify that it is fully secure.
