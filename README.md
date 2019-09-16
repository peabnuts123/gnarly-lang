# Gnarly - A programming language

Gnarly is a [Postfix](http://wiki.c2.com/?PostfixNotation) or "Reverse Polish Notation" programming language. It is an interpreted language, so it does not compile down to anything (no, not even JIT compile...) before execution, the symbols in the source code are interpreted and executed by the executing environment.

Gnarly could be considered an esoteric language, written for educational and entertainment purposes only, but it's also designed with real-world applications in mind, if you so wished to use such a "gnarly" language in a real project.

## Language design

Gnarly is a Postfix language. That means the order of operands/operators is written like this:

```shell
1 2 +
```

instead of the more-familiar "Infix" style:
```shell
1 + 2
```

The reason for this is that it makes it incredibly easy for machines to parse, and is also completely unambiguous in terms of order of operations (there is no need for parentheses, every unique set of operations has only 1 possible interpretation). Postfix languages often think of the program code as a "stack" of tokens (tokens being any kind of instruction or value separated by a space, usually), where new tokens can be "popped" off or "pushed" onto the stack as the program executes.
In Gnarly, almost everything is either an operand (a value to operate with e.g. `1`, `"hello"`, `$username` etc.) or an operator (a procedure to perform, using operands e.g. `+`, `and`, `array.reverse` etc.). Take a look at this code example:

```shell
1 2 + $result set
```

In this example, the tokens `1` and `2` and `$result` are operands, and `+` and `set` are operators. The interpreter reads tokens from the left (the "top of the stack") and reads each token one-by-one. The token `1` doesn't do anything when it is read, as it's just an operand. Similarly, neither does `2`. Once we read the operator `+`, the interpreter is instructed to do something. The `+` operator takes 2 operands and so pops `1` and `2` off the stack (as well as the operator itself, `+`) and pushes back on the result of adding the two operands: `3`, leaving the stack in the state:
```shell
3 $result set
```
the instruction pointer is now pointing at `$result` which, as a variable, does nothing. The operator `set` is then read and, just like the `+` operator, takes two operands. `3` and `$result` are popped off the stack (as well as `set`) the the value `3` is assigned to the variable `$result`. Now the stack is empty, so the program ends.

Examples of built-in operators include:
  * `+` - add two numbers
  * `/` - divide two numbers
  * `if` - begin processing an if-else block
  * `define_block` - begin definition of an execution block
  * `exit`  - exit the running program

## Code examples
Let's take a look at a few code examples. Since Gnarly isn't a real language (yet), syntax highlighting is a bit limited. `shell` highlighting has been used as an approximation.


### Hello world
```shell
# Simple hello world example

# `print` operator takes N operands and prints them to stdout, separated by a space
# Prints "Hello, world 1 2 3"
"Hello," "world!" 1 2 3 print
```

### Variables and operators

```shell
# Assign `2` to a variable named $two
# It doesn't matter much whether $two has been defined before
2 $two set
3 $three set

# Perform $two + $three, then store the result in $result
$two $three + $result set

# Subtract $two from $three, then print the result
$three $two - print

# `type_of` returns the type of its param, $result
# Prints "number" (as the value of $result is `5`)
$result type_of print

# Exit the application with code 0
0 exit
```

### Working with arrays

```shell
# Define an array called $array
[] $array set
# `array.push` pushes all of its operands to the end of the array
#   specified by the last operand, $array
1 2 3 4 5 6 $array array.push

# Prints "Array: 1, 2, 3, 4, 5, 6"
"Array: " $array print

# `array.pop` removes the last element from the end of the array
$array array.pop

# Prints "Array: 1, 2, 3, 4, 5"
"Array: " $array print
```

### Custom operators

In Gnarly, you can define custom operators, akin to functions or subroutines in other languages. If you really want, you can even override an existing operator. When declaring an operator, you declare its name, how many operands it takes, and what their names are. When your operator has finished processing, it can put values back on the stack or simply stop processing using the `return` operator.

```shell
# Define a custom operator called `square`
"square" define_operator
  # Declare 1 argument for this operator, called `num`
  "num" args
  # Perform $num multiplied by $num and push the result back onto the stack.
  # The `return` operator pops everything off the current stack preceding
  #   the `return` operator and stores them in memory. It then pops off the
  #   stack until it returns from the context of the operator (essentially,
  #   until it reaches the `end_operator` token), and then re-pushes everything
  #   the was preceding the `return` token, essentially "returning" those values.
  $num $num * return
end_operator

# The result from `square` is pushed onto the stack by the `return` statement
#   which is then consumed by `print`.
# Prints "9"
3 square print

# Create an `add1` operator by currying the `+` operator
"add1" define_operator
  "a" args
  1 $a + return
end_operator

# Unsurprisingly, prints "11"
10 add1 print
```

### Named blocks

Named blocks are similar to other languages like Java, except you can call the special operator `loop` which is similar to a `goto`. Named blocks in conjunction with the `loop` operator are the primary mechanism for looping in Gnarly. Once defined, named blocks are executed immediately in the place they are defined. Named blocks can be thought of as a custom operator that you invoke immediately after defining, and conditionally recursively invoke in-place of the `loop` operator. Named blocks can also be used with the `break` operator, which, instead of jumping to the start of the block, jumps to the end of the block.

```shell
# Declare a variable used for iteration
0 $i set

# Define block called `my_block`
"my_block" define_block
  # Print "Hello: $i"
  "Hello: " $i print

  # Add 1 to $i, then set the result to $i
  $i 1 + $i set

  # Loop the block `my_block` if the condition is true
  # In this case, the condition is checking whether $i is less than 5
  $i 5 lt "my_block" loop
end_block

```


### Merge sort

Here is a more comprehensive example of some "real world" programming, an implementation of [merge sort](https://en.wikipedia.org/wiki/Merge_sort). The implementation below is extra-comprehensive because it's an in-place merge sort, it modifies the array in-place, using `array.swap`.

```shell
"merge_sort" define_operator
  "start_index" "end_index" "array" args

  $end_index $start_index - $length set

  $length 1 lte if
    # array slice is just 1 or fewer elements, therefore it is in-order
    return
  else $length 2 eq if
    # array slice is just 2 elements
    # swap elements if first element is greater than second element
    $array[$start_index] $array[$start_index 1 +] > if
      $start_index $start_index 1 + $array array.swap
    endif

    return
  else
    # array slice is more than 2 elements, halve slices and merge sort each slice
    # find middle point
    $length 2 / math.floor $pivot set

    # merge sort left-hand side
    $start_index $pivot $array merge_sort
    # merge sort right-hand side
    $pivot $end_index $array merge_sort

    # two halves are sorted now, just need to fold them in together
    # smallest elements are first
    0 $left_pointer set
    $pivot $right_pointer set

    "fold_halves" define_block
      $array[$left_pointer] $array[$right_pointer] > then
        # left is bigger than right
        # move right to the left of left
        $right_pointer $swap_pointer set

          # perform array swaps until $swap_pointer is less than $left_pointer
        "move_index" define_block
          $swap_pointer 1 - $swap_pointer_minus_one set
          $swap_pointer $swap_pointer_minus_one $array array.swap
          $swap_pointer_minus_one $swap_pointer set
          $swap_pointer $left_pointer gte "move_index" loop
        end_block

        # increment right pointer
        $right_pointer 1 + $right_pointer set
      endif

      # increment left pointer
      $left_pointer 1 + $left_pointer set

      # Do not loop if left pointer has reached the end
      $left_pointer $end_index lt "fold_halves" loop
    end_block

    return
  endif
end_operator

# Declare array and push some values to it
[] $arr set
1 5 2 10 6 $arr array.push

# Sort the whole array
0 $arr["length"] $arr merge_sort

# Prints "1 2 5 6 10"
$arr print
```

## Backlog

  * Document standard lib, grammar, and built-in operators