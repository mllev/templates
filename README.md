# a.dom

Advanced Data Oriented Markup

ADOM is a language that compiles to HTML, has a terse syntax and a strong focus on data.

This is what tags looks like:
```
html [
  head [

  ]
  body [

  ]
]
```

This would of course compile to:
```html
<html>
  <head></head>
  <body></body>
</html>
```

ADOM has no whitespace sensitivity. The following is equally valid:
```
html[head[]body[]]
```

Text nodes are denoted using pipes:
```
html [
  head [
    title [
      | Page Title |
    ]
  ]
]
```

If a text node is the only child, the brackets may be omitted:
```
html [
  head [
    title | Page Title |
  ]
]
```

Tag attributes are the same as in html:
```
a href='/' target='_blank' | click me |
```

Void tags (meta, input, img, etc) are ended with semicolons:
```
html [
  head [
    title | Page Title |
    meta name='description' content='page description';
  ]
]
```

This makes for a very terse form syntax:
```
form action='/login' method='POST' [
  input name='username' type='text;
  input name='password' type='password';
  input type='submit' value='submit';
]
```

Data is a first class citizen of ADOM:
```
const pageTitle 'Page Title'

html [
  head [
    title | #{pageTitle} |
    meta name='description' content='page description';
  ]
]
```
