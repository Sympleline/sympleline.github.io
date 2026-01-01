# Javascript Markup Object - JSMO

JSMO is fast and light-weight UI library that focuses on having a great developer expirience whilst maintaining great performance. JSMO keeps things simple by taking a "UI as data" aproach, using plain old javascript objects to declare the structure of your UI, no need to learn new fancy complicated syntax.

At its core JSMO attempts to give developers the ability to rapidly create components, style them and add events to them and access there data effeciently.

## Usage

### Creating An Element
JSMO provides a namespace (`namespace j {...}`) wich contains HTML element constructor function for all all types of HTML elements.
```js
// Create a heading
j.h1({})

// A paragraph
j.p({})
```

For developer convinience JSMO uses shorthand tag names for the following HTML elements.
```js
// Instead of:
j.button({})
j.span({})
j.select({})
j.option({})

// Use:
j.btn({})
j.spn({})
j.slct({})
j.optn({})
```

JSMO also has a raw `jsmo(tag, properties)` HTML element constructor function thats used internally but can be used by developers though it is not recomended to use this! use the `j` namespace instead.
```js
jsmo(`button`, {}) // Not recomended!

j.btn({}) // Recomended
```

### Adding Content To Elements

#### String Content
They are two ways to add string content to an element, the standard way and the shorthand way
1. ##### Standard way:
    ```js
    j.h1({in: `Hello World`})
    
    // Or

    jsmo(`h1`, {in: `Hello World`})
    ```

2. ##### Shorthand:
    ```js
    j.h1({}, `Hello World`)

    //Or

    jsmo(`h1`, {}, `Hello World`)
    ```

### Adding Children To Elements
To add children to elemnts simply assign an object or array with other JSMO elements to the `in` property:
```js 
j.div({
    in: {
        button: j.btn({}, `Click Me!`)
    }
})

// Or

j.div({
    in: [ j.btn({}, `Click Me!`) ]
})
```
This will not work!!

```js
j.div({}, { button: j.btn({}, `Click Me!`)}) // Nope, this wont work!!
j.div({}, [j.btn({}, `Click Me!`)]) // This wont work either.
```
The last parameter of an Element constructor function can only take strings as an argument.

### Adding Attributes To Elements
To add an attribute to your element simply use the attribute name suffixed with an underscore `id_`. Attributes are suffixed with an underscore so they can be identified from other properties.

```js
const main = j.main({
    id_: `my-id`,
    class_: `my-class container`
})

// Access an attribute
console.log(main.id_) // Outputs: 'my-id'

// Setting an attribute
main.id_ = `new-id`
```
Attributes in JSMO are bound both ways, e.g if a user types into the input Element of a JSMO, the data in the input can be accessed by accessing the `value_` attribute on the JSMO it belongs to, like wise, setting the `value_` property on a JSMO updates it on its HTML element aswell.

### Class Attribute
The class attribute on JSMO elements works differently from other attributes
#### Setting
```js
const button = j.btn({
    class_: `btn btn-dark xxl`
})
```

#### Accessing
```js
console.log(button.class_) // Outputs: 'btn btn-dark xxl'
```

#### Updating
```js

// Adding
button.classList.add(`bg-danger`);

// Checking
button.classList.has(`btn`) // Returns true

// Removing
button.classList.remove(`xxl`);

// This will have no effect!!
button.class_ = `some classes`;
```

### Adding Events
Events in JSMO are just methods in the object named after the event you wish to set. The method will recieve the event object as a parameter.

```js
j.btn({
    class_: `btn`,
    in: `Click Me`
    click(e){
        console.log(e);
    }
})

// Or

j.btn({
    class_: `btn`,
    in: `Click Me`
    click: function(e){
        console.log(e);
    }
})
```
Notice how events are defined without 'on' at the begining i.e `click` instead of `onclick`.

### Styling £lements
You can style you elements as usual with css, but JSMO provides a powerful way to style you elements.

```js
j.btn({
    class_: `btn`,
    in: `Click Me`,
    style: {
        color: `red`
    }
})
```

#### Styling element descendents
```js
j.btn({
    class_: `btn`,

    // The styles object works just a normal css style rule
    // with a few critical differences!
    style: {
        border: `0`,
        paddin: `1em`,
        background: `yellow`,

        //Apply a hover effect to the element, DO NOT ADD `&` i.e `&:hover to psuedos!!`
        ':hover': {
            color: `white`
        },

        '::selection': {
            background: `none`
        },

        // When styling decendents add `&` before the selector i.e:
        '& i': {
            marginRight: `5px`,
        },

        '& span': {
            fontWeight: `bold`
        },

        '& span & i': {
            color: `red`,

            '& .nested-children': {
                // Style deeper nested descendents
            }
        }

        // You can use any valid css selector just remember, when applying to chldren add `&` not when applying psuedos 
        '& > :first-child, ::nth-child(1), .child':{
            //styles
        }
    },
    in: {
        icon: j.i({
            class_: `fi fi-rr-star`
        }),

        text: j.spn({
            in: `Click Me`
        })
    }
})
```

### Reactive Variables
#### Definition
Defining a reactive variable
```js
const count = $.var(0);

// Accessing the value
count.value;

// Setting the value
count.value = 1;
```

#### Using in components
In this example the text content of the button automatically updates when the value of `count` is incremented.
```js
function component () {
    const count = $.var(0);
    return j.btn({
        in: count,
        click () {
            count.value++
        }
    })
}
```

#### Using in attributes
```js
function inputComponent () {
    const count = $.var(0);
    return j.input({
        type_: `number`,
        value_: count,
    })
}
```
When a user type into the input, the value of the reactive variable will be updated, so will any other components that use the variable. Updating the value of the reactive variable will also update the value of attributes of JSMOs that have been assigned with the variable.

### Accessing JSMO Elements
1. #### Using dot (.) notation
    You can access a nested jsmo element with dot notaion.
    ```js
    const parent = j.div( {
        id_: `parent`,
        class_: `container`,
        in: {
            button: j.btn( {
                id_: `btn`,
            } )
        }
    } )

    const btn = parent.in.button;
    ```
    This is not very practicle when working the large components with multiple descendents.

2. #### Using `getBy` Utility Namespace
    ```js
    // 1. Get a JSMO by id. returns a JSMOElemnt
    getBy.id(`btn`);

    // 2. Get a JSMO by class. returns a JSMOElement set
    getBy.class(`container`);

    // 3. Get a JSMO by tag. returns a JSMOElement set
    getBy.tag(`button`);
    ```

### Creating Components
To create a component simply create a function that returns a JSMO element
```js
function app(){
    return j.main({
        id_: `app`,
        in: {
            sidebar: j.aside({}),
            content: j.main({})
        }
    })
}
```

### USing Props
In JSMO props are simply parameters of any data passed to a function component
```js
function app(id, content){
    return j.main({
        id_: id,
        in: content,
    })
}

app(`app`, {
    sidebar: j.aside({}),
    content: j.main({})
})
```

### JSMO Utilites

1. #### Adding children to already existing JSMOs
    ```js
     const parent = j.div( {
        id_: `parent`,
        class_: `container`,
        in: {
            button: j.btn( {
                id_: `btn`,
            } )
        }
    } )

    const child = j.btn( {
        id_: `btn 2`,
    } )

    parent.addChild(child)
    ````

2. #### Deleting a JSMO
    ```js
    const parent = j.div( {
        id_: `parent`,
        class_: `container`,
        in: {
            button: j.btn( {
                id_: `btn`,
            } )
        }
    } )

    parent.delet();
    ```

3. #### Updating the styles of JSMO element
    ```js
    const parent = j.div( {
        id_: `parent`,
        class_: `container`,
        in: {
            btn: j.btn( {
                id_: `btn`,
            } )
        }
    } )

    parent.$({
        padding: `1em`,
        '& button': {
            border: `0`
        }
    })
    ```

4. #### Bulk updating a JSMO
    ```js
    const parent = j.div( {
        id_: `parent`,
        class_: `container`,
        in: {
            btn: j.btn( {
                id_: `btn`,
            } )
        }
    } )

    parent.__u({
        id_: `super-parent`,
        class_: `super-container`, // Classes added here will be add to the existing classes of the JSMO being updated.
        style: {
            padding: `0`,

            '& button': {
                border: `1px solid gray`
            }
        }
    })
    ```

### Mounting An App
To mount an app call the `JSMO.loadApp` static method of the JSMO class, it takes 2 parameters, the first is a JSMO component, the second is HTML element to which the app will be mounted.
```js
function app(){
    return j.main({
        id_: `app`,
        in: {
            sidebar: j.aside({}),
            content: j.main({})
        }
    })
}

JSMO.loadApp(app(), document.body);
```

### TODO

1. Create reactive lists - a reactive variable that can hold a list of JSMO elements
    ```js
    const list = $.list([
        j.li({}, `list item 1`), 
        j.li({}, `list item 2`),
        j.li({}, `list item 3`)
    ])

    const ul = j.ul({
        class_: `list`,
        in: list
    })

    // Add an element
    list.append(j.li({}, `list item 4`));
    list.prepend(j.li({}, `list item 0`));

    // Removing an element
    list.delete(1) // with index
    list.delete(j.li({}, `list item 1`)) // with a JSMO element

    // Empy list the list
    list.empty()
    // or
    ul.empty()
    ```
