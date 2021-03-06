### Defining Classes

To define a new Ember _class_, call the `extend()` method on
`Ember.Object`:

```javascript
Person = Ember.Object.extend({
  say: function(thing) {
    alert(thing);
  }
});
```

This defines a new `Person` class with a `say()` method.

You can also create a _subclass_ from any existing class by calling
its `extend()` method. For example, you might want to create a subclass
of Ember's built-in `Ember.View` class:

```app/views/person.js
PersonView = Ember.View.extend({
  tagName: 'li',
  classNameBindings: ['isAdministrator']
});
```

When defining a subclass, you can override methods but still access the
implementation of your parent class by calling the special `_super()`
method:

```javascript
Person = Ember.Object.extend({
  say: function(thing) {
    var name = this.get('name');
    alert(name + " says: " + thing);
  }
});

Soldier = Person.extend({
  say: function(thing) {
    // this will call the method in the parent class (Person#say), appending
    // the string ", sir!" to the variable `thing` passed in
    this._super(thing + ", sir!");
  }
});

var yehuda = Soldier.create({
  name: "Yehuda Katz"
});

yehuda.say("Yes"); // alerts "Yehuda Katz says: Yes, sir!"
```

### Creating Instances

Once you have defined a class, you can create new _instances_ of that
class by calling its `create()` method. Any methods, properties and
computed properties you defined on the class will be available to
instances:

```javascript
var person = Person.create();
person.say("Hello"); // alerts " says: Hello"
```

When creating an instance, you can initialize the values of its properties
by passing an optional hash to the `create()` method:

```javascript
Person = Ember.Object.extend({
  helloWorld: function() {
    alert("Hi, my name is " + this.get('name'));
  }
});

var tom = Person.create({
  name: "Tom Dale"
});

tom.helloWorld(); // alerts "Hi, my name is Tom Dale"
```

For performance reasons, note that you cannot redefine an instance's
computed properties or methods when calling `create()`, nor can you
define new ones. You should only set simple properties when calling
`create()`. If you need to define or redefine methods or computed
properties, create a new subclass and instantiate that.

By convention, properties or variables that hold classes are
PascalCased, while instances are not. So, for example, the variable
`Person` would point to a class, while `person` would point to an instance
(usually of the `Person` class). You should stick to these naming
conventions in your Ember applications.

### Initializing Instances

When a new instance is created, its `init` method is invoked
automatically. This is the ideal place to do setup required on new
instances:

```js
Person = Ember.Object.extend({
  init: function() {
    var name = this.get('name');
    alert(name + ", reporting for duty!");
  }
});

Person.create({
  name: "Stefan Penner"
});

// alerts "Stefan Penner, reporting for duty!"
```

If you are subclassing a framework class, like `Ember.View` or
`Ember.ArrayController`, and you override the `init` method, make sure
you call `this._super()`! If you don't, the system may not have an
opportunity to do important setup work, and you'll see strange behavior
in your application.

### Accessing Object Properties

When accessing the properties of an object, use the `get`
and `set` accessor methods:

```js
var person = Person.create();

var name = person.get('name');
person.set('name', "Tobias Fünke");
```

Make sure to use these accessor methods; otherwise, computed properties won't
recalculate, observers won't fire, and templates won't update.
