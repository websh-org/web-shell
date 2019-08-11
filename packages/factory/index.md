# factory

**https://github.com/websh-org/factory**

The component factory for WebShell, featuring multiple inheritance and and extendable building system.

## Contents
* [Synopsis](#synopsis)
* [Basic usage](#basic-usage)
  + [Registering a component type](#registering-a-component-type)
  + [Creating a component](#creating-a-component)
  + [Inheriting component types](#inheriting-component-types)
    - [Adding members](#adding-members)
    - [Overriding members](#overriding-members)
    - [Adding init functions](#adding-init-functions)
  + [Multiple inheritance](#multiple-inheritance)
    - [Creating components with multiple types](#creating-components-with-multiple-types)
    - [Inheriting multiple types in a type.](#inheriting-multiple-types-in-a-type)
* [Extending the factory with custom builders](#extending-the-factory-with-custom-builders)
  + [Advanced builders](#advanced-builders)

## Synopsis
````bash
$ npm install @websh/factory
````
````js
import { register, create } from "@websh/factory";

register("my-type", { ... });
const compontent = create("my-type", { ... })
````

## Basic usage

- Use `create(typeList, args)` to create a component that inherits one of more types.

### Registering a component type
Use `register(type, typeDef)` to register a component type. 

<dl><dt>
  type
</dt><dd>
  Must be a string containing a single word, i.e. no whitespace.
</dd><dt>
  typeDef
</dt><dd>
Must be an object. It can have the following properties:
  <dl><dt>
    private
  </dt><dd>
    An object containing the component's private members. These will be accessable under `this` in the component's methods, but not accessible from the outside. Any supplied methods will be bound to the component.
  </dd><dt>
    public
  </dt><dd>
    An object containing the component's public members. These will be accessable under `this.public` in the component's methods, and under ``.
  </dd></dl>
</dd></dl>

````js
import { register } from "@websh/factory";

register('hello', {
  private: {
    greeting: "Hello",
    name: "world",
    output(message) {
      console.log(this.name, message)
    }
  },
  public: {
    greet() {
      this.output(`${this.greeting}, ${this.name}!`);
    }
  },
  init({ greeting, name }) {
    this.greeting = greeting || this.greeting;
    this.name = name || this.name;
  }
})

````
### Creating a component
Use `create(type, ...args)` to create a component that will inherit one or more type. 
* `type` must be a registered component type.
* `args` will be passed to the `init` functions specified in the typedefs of various types.

`create` returns only the component's public interface, defined with the `public` option and potentially custom builders that your component types provide. See below for details on builders.

````js
import { create, test } from "@websh/factory";

const world = create("hello", {})
test(
  "hello.greeting is private", 
  !("greeting" in world)
);
test(
  "hello.name is public", 
  !("greeting" in world)
);
test(
  "The default greeting is", "Hello, world!", 
  world.greet()
)
````
By default, public members are settable from outside the component:
````js
const aunty = create("hello", { name: "Aunty" })
test(
  "The custom greeting is", "Hello, Aunty!", 
  aunty.greet()
)

aunty.name = "Uncle";
test(
  "hello.name is settable", 
  aunty.name === "Uncle"
);
test(
  "The new greeting is", "Hello, Uncle!", 
  aunty.greet()
);
````

### Inheriting component types
Use the `extends` option in the type definition to make all components of this type inherit another type. 

#### Adding members
The type definition can add `public` and `private` members to the component:

````js
register("ask", {
  extends: "hello",
  public: {
    ask(question) {
      return this.output(`Hey ${this.public.name}, ${question}?`);
    }
  }
})
const ask = create('ask', { name: "Buddy" })

test(
  "The greeting is", "Hello, Buddy!", 
  ask.greet()
)
test(
  "The question is", "Hey Buddy, what's up?", 
  ask.ask("what's up")
);
````
> Inheritence order: `hello`, `ask`

#### Overriding members
You can override `public` and `private` members of the inherited type:

````js
register("hi", {
  extends: "hello",
  private: {
    greeting: "Hi"
  }
})
const hi = create('hi', { name: "Mom" })
test(
  "The greeting is", "Hi, Mom!", 
  hi.greet()
)
````
> Inheritence order: `hello`, `hi`

#### Adding init functions

`init` functions for each type are ran after members defined in that type have been added or overriden. 

Type definitions are applied to components depth-first, meaning that members of each inherited type will be added and its `init` function executed before members in the child type are added. 
> This means that each `init` function can rely on the expected state of the object when it's executed.


````js
register("greeting", {
  extends: "hello",
  init({greeting = this.greeting}) {
    this.greeting = greeting;
  }
})


const kiddo = create('greeting', {
  greeting: "Hey",
  name: "Kiddo"
})
test(
  "The greeting is", "Hey, Kiddo!",
  kiddo.greet()
)

````
> Inheritence order: `hello`, `greeting`

### Multiple inheritance

Both `create` and the `extends` option accept type lists as well as
types. 

The type list can be:
* a string with one type
* a string with space-separated types, 
* an array of type lists

All of the following are acceptable values:
  * `"type1 type2 type3"`
  * `["type1", "type2", "type3"]`
  * `["type1 type2", "type3"]`
  * `["type1", ["type2", "type3"]]`
  * `[["type1"], ["type2 type3"]]`

Each type is inherited only once. Types are applied from left to right, when specified either in `create` or in the `extends` option. Types are inherited depth first, with no repetition. The `init` function is executed after all the types specified in `extends` are inherited and their `init` functions are run. 

#### Creating components with multiple types
Use `create(typeList, ...args)` to create a component that will inherit one or more types. 

````js
const mister = create("greeting ask", {
  greeting: "Good day",
  name: "Mister"
}) 
test(
  "The greeting is", "Good day, Mister!", 
  mister.greet()
)
test(
  "The question is", "Hey Mister, who do you think you are?", 
  mister.ask("who do you think you are")
)
````
> Inheritence order: `hello`, `greeting`, `ask`

#### Inheriting multiple types in a type.
The `extends` option in the type definition accept type lists as well as types. 

````js
const dear = create('dear', { name: "Mother" })
test("The greeting is", "Hello, dear Mother!", dear.greet())

register("person", {
  extends: "greeting ask",
  public: {
    warn(about) {
      return this.output(`Watch out for ${about}, ${this.public.name}!`);
    }
  },
});

const doc = create('person', { name: "Doc" });
test(
  "The greeting is", "Hi, Doc!", 
  doc.greet()
);
test(
  "The question is", "Hey Doc, what's cooking?",
  doc.ask("what's cooking")
);
test(
  "The warning is", 
  "Watch out for the dog, Doc!", 
  doc.warn("the dog")
);
````
> Inheritence order: `hello`, `greeting`, `ask`, `person`

## Extending the factory with custom builders
Builders are functions that build the component based on the options specified in the type definition. In addition to the builtin `private` and `public` builders, your type definition can define custom builders that can be used by types that inherit it.

````js
// add the "methods" builder for methods that will be accessable both on this and
// on the component's public interface.
register("methods", {
  build: {
    methods(def) {
      // def is the "methods" option in the type definition
      for (var name in def) {
        this[name] = this.public[name] = def[name].bind(this);
      }
    }
  }
})
register("officer", {
  extends: "methods",
  methods: {
    title() {
      return `${this.rank} ${this.name}`
    }
  },
  private: {
    name: "",
    rank: "Captain",
    address(message) {
      // this.title works in a private function
      return `${this.title()}! ${message}, sir!`;
    }
  },
  public: {
    // this.title works in a private function
    yessir() {
      return `Yes sir, ${this.title()}!`
    },
    excuse(reason) {
      return this.address(`I was just ${reason}`)
    }
  },
  init({ rank = this.rank, name = this.name }) {
    this.rank = rank;
    this.name = name;
  }
})

const captain = create("officer", { name: "Darling" });

//captain.title works on the public interface
test(
  "The title is", "Captain Darling",
  captain.title()
)

test(
  "The yessir is", "Yes sir, Captain Darling!",
  captain.yessir()
)

test(
  "The excuse is",
  "Captain Darling! I was just cleaning my gun, sir!",
  captain.excuse("cleaning my gun")
)
````
### Advanced builders
Instead of providing just the build function, you can provide an object:
````js
register('my-type',{
  build: {
    stuff: {
      build(def,dd) {
        // def is the "stuff" option in the type definition
        // dd is an object that will be shared between build and init functions
        ...
      },
      init(def,dd, ...args) {
        // def is the "methods" option in the type definition
        // dd is an object that will be shared between build and init functions
        // args are args specified in create(...)

        // this will be run when a type contains the "stuff" option
        // it will be run after build functions of all builders and before the
        // init function for the type
        ...
      },
    }
  }
})

````
Builders can be augmented in types that are inherited later.
````js
register('my-type2',{
  extends: "my-type"
  build: {
    stuff: {
      build(def,dd) {
        // this will be run after the "stuff" bulder in "my-type"
        // dd is shared between types
        ...
      },
      init(def,dd, ...args) {
        // this will be run after the "stuff" init function in my-type
        // dd is shared between types
        ...
      },
    }
  }
})

````

