<h1 align="center">
	<br>
    <img width="200" src="https://raw.githubusercontent.com/mllev/a.dom/master/logo.png">
    <br>
</h1>

## a.dom

Advanced Data Oriented Markup

ADOM is a templating language with extremely advanced features.

In just 2k lines of code, with no dependencies, and a single function API, ADOM packs in:

- a templating language that looks and feels more like a programming language, providing a robust and predictable syntax for HTML
- a high speed reactive UI engine using plain Javascript, with no modifications made to your code (making it fully compatible with Javascript preprocessors, and the full ecosystem of tools)
- server side rendering that is simpler, faster, and *far* easier to understand than all modern solutions
- code separation and bundling via simple import/export semantics

#### GETTING STARTED

Getting started with ADOM is very easy.

```
npm install adom-js
```

First, create a basic node server.
```js
require('http').createServer((req, res) => {
    res.writeHead(200, { 'Content-type': 'text/html' });
    res.end('<h1>Hello!</h1>');
}).listen(5000);
```

Next, create an instance of the ADOM compiler.
```js
const adom = require('adom-js');
const A = new adom({
    rootDir: '.' // tell adom where to look for adom files
});

require('http').createServer((req, res) => {
    res.writeHead(200, { 'Content-type': 'text/html' });
    res.end('<h1>Hello!</h1>');
}).listen(5000);
```

Then, create an `index.adom` file in the same directory as your server file.
```
doctype html5

html [
    head []
    body [
        h1 | Welcome back, {name}! |
    ]
]
```

Finally, serve your `index.adom` file.
```js
const adom = require('adom-js');
const A = new adom({
    rootDir: '.' // tell adom where to look for adom files
});

require('http').createServer((req, res) => {
    res.writeHead(200, { 'Content-type': 'text/html' });
    res.end(A.render('index.adom', { name: 'Matt' }));
}).listen(5000);
```

#### GUIDE
##### SYNTAX

Tags in ADOM look like this:
```
div attr1='val1' attr2='val2' [
    div []
    div []
]
```
ADOM has no whitespace sensitivity at all:
```
div
    attr1='val1'
    attr2='val2' [ div [] div [] ]
```
Attribute syntax is largely the same as HTML. Each attribute takes the form `<attribute> '=' <string>`. ADOM supports both single and double quote strings for attributes.
```
div attr1="val1" attr2="val2" []
```
You can use the `.` shorthand for classes:
```
div.class1.class2 []
```
Self-closing tags are ended with semicolons instead of brackets:
```
img src="/img.png";
```
Pipes are used to denote text nodes:
```
h1 [
    | I AM SOME TEXT! |
]
```
If a textnode is the only child of a tag, the brackets may be omitted:
```
h1 | I AM SOME TEXT! |
```
##### FEATURES

As demonstrated above, data can be passed to an ADOM template from the server:
```js
// server.js
const adom = require('adom-js');
const A = new adom({
    rootDir: '.' // tell adom where to look for adom files
});

require('http').createServer((req, res) => {
    res.writeHead(200, { 'Content-type': 'text/html' });
    res.end(A.render('index.adom', { name: 'Matt' }));
}).listen(5000);
```
The `name` variable is now available for interpolation:
```
// index.adom
doctype html5

html [
    head []
    body [
        h1 | Welcome back, {name}! |
    ]
]
```
Data can also be declared directly in the file:
```
const name = 'matt'

doctype html5

html [
    head []
    body [
        h1 | Welcome back, {name}! |
    ]
]
```
ADOM supports the following data types:
```
const isHuman = true // boolean
const name = 'matt' // string
const age = 100 // numbers

// object
const person = {
    name: 'matt'
}

// array
const people = [
    'matt',
    'bob'
]

doctype html5

html [
    head []
    body [
        h1 | Welcome back, {name}! |
    ]
]
```
Data must be declared in the top level of the document. The following will not work:
```
div [
    var x = 5
    span | {x} |
]
```
ADOM supports control flow:
```
doctype html5

const items = [
    'walk dog',
    'feed dog',
    'buy dog food'
]

html [
    head []
    body [
        h1 | TODO LIST |
        ul [
            each (item in items) [
                li |{item}|
            ]
        ]
        if (items.length == 0) [
            p | Good job! |
        ] else [
            p | Lots of work to do... |
        ]
    ]
]
```
`each` can takes an optional second argument:
```
each (item, i in items) [
    // i is the index
]
```
`each` can loop over objects too:
```
doctype html5

const items = {
    item1: 'walk dog',
    item2: 'feed dog',
    item3: 'buy dog food'
}

html [
    head []
    body [
        h1 | TODO LIST |
        ul [
            each (key, value in items) [
                li |{key}: {value}|
            ]
        ]
    ]
]
```
Data can come from external files too:
```
doctype html5

const styles = file 'style.css'

html [
    head [
        style |{styles}|
    ]
    body [
        // ...
    ]
]
```
ADOM supports custom tags using the `tag` keyword. Props are passed to tags using the attribute syntax documented above, and are used from within the tag using the `props` keyword:
```
doctype html5

tag ListItem [
    li |{props.item}|
]

tag TodoList [
    ul [
        each (item in props.items) [
            ListItem item={item};
        ]
    ]
]

html [
    head []
    body [
        h1 | TODO LIST |
        TodoList items={[
            'walk dog',
            'feed dog'
        ]};
    ]
]
```
Tags can either be self-closed using `;` if they don't have children. If you would like to add children to a tag, you use the yield keyword:
```
doctype html5

tag TestTag [
    p | inside the tag 1 |
    yield
    p | inside the tag 2 |
]

html [
    head []
    body [
        TestTag [
            p | tag child |
        ]
    ]
]
```
This compiles to the following html:
```html
<!DOCTYPE html>
<html>
    <head></head>
    <body>
        <p>inside the tag 1</p>
        <p>tag child</p>
        <p>inside the tag 2</p>
    </body>
</html>
```