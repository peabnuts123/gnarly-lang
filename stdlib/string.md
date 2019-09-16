# Stdlib: string

### `string.uppercase`
```
<a:string> string.upppercase := <b:string>

Transforms string `a` into all uppercase and pushes the result onto the stack.

Example:
# $str is "Hello world"

$str string.uppercase print
# prints "HELLO WORLD"
```

### `string.lowercase`
```
<a:string> string.lowercase := <b:string>

Transforms string `a` into all lowercase and pushes the result onto the stack.

Example:
# $str is "HELLO world"

$str string.lowercase print
# prints "hello world"
```

### `string.reverse`
```
<a:string> string.reverse := <b:string>

Reverses string `a` and pushes the result onto the stack.

Example
# $str is "Hello world"

$str string.reverse print
# prints "dlrow olleH"
```

### Backlog
  * string.split