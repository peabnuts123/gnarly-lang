# Stdlib: array

This is a WIP and super not finalised. It's more of a notepad for thoughts.

## `array.append`
```bash
%array.append define_operator
  * $value arg
  *[] $array arg
begin
  # ...
end
```

Push a single value `$value` onto the end of array `$array`.

### Example
```bash
number[] $array [1 2 3] declare
4 $array array.append
# $arr is now [1 2 3 4]
```

### See also
  * [`array.prepend`](#arrayprepend)

## `array.prepend`
```bash
%array.prepend define_operator
  * $value arg
  *[] $array arg
begin
  # ...
end
```

Push a single value `$value` onto the start of array `$array`.

### Example
```bash
number[] $array [2 3 4] declare
1 $array array.prepend
# $arr is now [1 2 3 4]
```

### See also
  * [`array.append`](#arrayappend)

## `array.push`
```bash
%array.push define_operator
  * $value arg
  *[] $array arg
begin
  # ...
end
```

Synonym for [`array.append`](#arrayappend). Push a single value `$value` onto the end of array `$array`.

### Example
```bash
number[] $array [1 2 3] declare
4 $array array.push
# $arr is now [1 2 3 4]
```

### See also
  * [`array.pop`](#arraypop)

## `array.enqueue`
```bash
%array.enqueue define_operator
  * $value arg
  *[] $array arg
begin
  # ...
end
```

Synonym for [`array.append`](#arrayappend). Push a single value `$value` onto the end of array `$array`.

### Example
```bash
number[] $array [1 2 3] declare
4 $array array.enqueue
# $arr is now [1 2 3 4]
```

### See also
  * [`array.dequeue`](#arraydequeue)

## `array.pop`
```bash
%array.pop define_operator
  *[] $array arg
  * result
begin
  # ...
end
```

Pop the last value from the end of the array `$array` and push the popped value onto the stack.

### Example
```bash
number[] $array [1 2 3 4] declare
$array array.pop
# $array is now [1, 2, 3]
# stack is 4
```

### See also
  * [`array.push`](#arraypush)

## `array.dequeue`
```bash
%array.dequeue define_operator
  *[] $array arg
  * result
begin
  # ...
end
```

Dequeue the first value from the start of the array `$array` and push the dequeued value onto the stack.

### Example
```bash
number $array [1 2 3 4] declare
$array array.dequeue
# $array is now [2, 3, 4]
# stack is 1
```

### See also
  * [`array.enqueue`](#arrayenqueue)


### Backlog
  * _Nothing at present._