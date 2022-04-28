# Stdlib: string

This is a WIP and super not finalised. It's more of a notepad for thoughts.

## `string.uppercase`
```bash
%string.uppercase define_operator
  string $str arg
  string result
begin
  # ...
end
```

Transforms string `$str` into all uppercase and pushes the result onto the stack.

### Example:
```bash
string $str "Hello world" declare
# Prints "HELLO WORLD"
$str string.uppercase print
```

### See also
  * [`string.lowercase`](#stringlowercase)

## `string.lowercase`
```bash
%string.lowercase define_operator
  string $str arg
  string result
begin
  # ...
end
```

Transforms string `$str` into all lowercase and pushes the result onto the stack.

### Example:
```bash
string $str "HELLO world" declare
# Prints "hello world"
$str string.lowercase print
```

### See also
  * [`string.uppercase`](#stringuppercase)

## `string.reverse`
```bash
%string.reverse define_operator
  string $str arg
  string result
begin
  # ...
end
```

Reverses string `$str` and pushes the result onto the stack.

### Example
```bash
string $str "Hello world" declare
# Prints "dlrow olleH"
$str string.reverse print
```

## `string.split`
```bash
%string.split define_operator
  string $separator arg
  string $str arg
  string[] result
begin
  # ...
end
```

Split string `$str` into fragments separated by `$separator` and pushes the result (a string array) onto the stack. The separators are not returned in the array.

### Example
```bash
string $str "Hello world" declare
string[] $parts "o" $str string.split declare
# $parts is now ["Hell" " w" "rld"]

string $address "192.168.1.64" declare
string[] $segments "." $address string.split declare
# $segments is now ["192" "168" "1" "64"]
```

### Backlog
  * _Nothing at present._