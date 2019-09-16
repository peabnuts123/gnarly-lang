# Stdlib: array

### `array.push`
```
<value1:any> [...<valueN:any>] <a:array> array.push := void

Push values `value1` through `valueN` onto the end of array `a`.

Example:
# $arr is [1, 2, 3]

4 5 6 7 8 $arr array.push

# $arr is now [1, 2, 3, 4, 5, 6, 7, 8]
```

### `array.pop`
```
<a:array> array.pop := void

Pop the last value from the end of the array `a`. NOTE: does not put this value anywhere, it is just removed from the array.

Example:
# $arr is [1, 2, 3, 4]

$arr array.pop

# $arr is now [1, 2, 3]
# stack is empty
```

### `array.peek`
```
<a:array> array.peek := <value:any>

Take the last value from the end of the array and put it on the stack.

Example:
# $arr is [1, 2, 3]

$arr array.peek

# stack is now: 3
```

### `array.enqueue`
```
<value1:any> [...<valueN:any>] <a:array> array.enqueue := void

Enqueue values `value1` through `valueN` onto the start of the array `a`.

Example
# $arr is [6, 7, 8]

1 2 3 4 5 $arr array.enqueue

# $arr is now [1, 2, 3, 4, 5, 6, 7, 8]
```

### `array.dequeue`
```
<a:array> array.dequeue := void

Dequeue the first value from the start of the array `a`. NOTE: does not put this value anywhere, it is just remove from the array.

Example
# $arr is [1, 2, 3, 4]

$arr array.dequeue

# $arr is now [2, 3, 4]
```

### `array.is_array`
```
<a:any> array.is_array := <result:boolean>

Determine whether `a` is of type array and pushes the boolean result onto the stack.

Example:
[] $a set
20 $b set

$a array.is_array print
# prints "true"

$b array.is_array print
# prints "false"
```

### Backlog
  *