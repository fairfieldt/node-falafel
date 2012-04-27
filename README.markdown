falafel
=======

Transform the [ast](http://en.wikipedia.org/wiki/Abstract_syntax_tree) on a
recursive walk.

This module is like [burrito](https://github.com/substack/node-burrito),
except that it uses [esprima](http://esprima.org) instead of
[uglify](https://github.com/mishoo/UglifyJS)
for friendlier-looking ast nodes.

example
=======

array.js
--------

Put a function wrapper around all array literals.

``` js
var falafel = require('falafel');

var src = '(' + function () {
    var xs = [ 1, 2, [ 3, 4 ] ];
    var ys = [ 5, 6 ];
    console.dir([ xs, ys ]);
} + ')()';

var output = falafel(src, function (node) {
    if (node.type === 'ArrayExpression') {
        node.update('fn(' + node.source() + ')');
    }
});
console.log(output);
```

output:

```
(function () {
    var xs = fn([ 1, 2, fn([ 3, 4 ]) ]);
    var ys = fn([ 5, 6 ]);
    console.dir(fn([ xs, ys ]));
})()
```

methods
=======

``` js
var falafel = require('falafel')
```

falafel(src, fn)
----------------

Transform the string source `src` with the function `fn`, returning the
transformed string output.

For every node in the ast, `fn(node)` fires. The recursive walk is a
pre-traversal, so children get called before their parents.

Performing a pre-traversal makes it easier to write nested transforms since
transforming parents often requires transforming all its children first.

nodes
=====

Aside from the regular [esprima](http://esprima.org) data, you can also call
some inserted methods on nodes.

Aside from updating the current node, you can also reach into sub-nodes to call
update functions on children from parent nodes.

node.source()
-------------

Return the source for the given node, including any modifications made to
children nodes.

node.update(s)
--------------

Transform the source for the present node to the string `s`.

install
=======

With [npm](http://npmjs.org) do:

```
npm install falafel
```

license
=======

MIT