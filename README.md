# Gnarly - A programming language

Gnarly is a [Postfix](http://wiki.c2.com/?PostfixNotation) or "Reverse Polish Notation" programming language. It is an interpreted language, so it does not compile down to anything before execution, the symbols in the source code are interpreted and executed by the runtime.

Gnarly is considered an esoteric language written for educational and entertainment purposes only, but it's also designed with real-world applications in mind, so as to not be obtuse in its use.

## Language design

Gnarly is a Postfix language. That means the order of operands/operators are written like this:

```bash
1 2 +
```

instead of the more-familiar "Infix" style:
```bash
1 + 2
```

The reason for this is that it makes it incredibly easy for machines to parse `(Editor's note: I wrote this before starting work on the parser)`, and is also unambiguous in terms of order of operations (there is no need for parentheses so long as the number of operands is known). Postfix languages often operate with a "stack", where new tokens can be "popped" off or "pushed" onto the stack as the program executes.
In Gnarly, almost everything is either an operand (a value to operate with e.g. `1`, `"hello"`, `$username` etc.) or an operator (a procedure to perform, using operands e.g. `+`, `and`, `array.reverse` etc.). Take a look at this code example:

```bash
1 2 + $result set
```

In this example, the tokens `1`, `2` and `$result` are operands, and `+` and `set` are operators. The interpreter reads tokens from left to right and reads each token one-by-one. The token `1` pushes the value 1 onto the stack. Similarly, `2` pushes 2 onto the stack, leaving the stack like this:

```bash
2
1 # bottom of the stack
```

Once we read the operator `+`, the interpreter is instructed to do something. The `+` operator takes 2 operands, so it pops `2` and `1` off the stack and pushes back onto the stack the result of adding the two operands: `3`, leaving the stack in the state:

```bash
3 # bottom of the stack
```

the instruction pointer is now pointing at `$result` which, as a variable, pushes a reference to itself onto the stack:

```bash
$result # a variable identifier
3       # bottom of the stack
```

The operator `set` is then read and, just like the `+` operator, takes two operands. `$result` and `3` are popped off the stack, then the value `3` is assigned to the variable `$result`. Now we've reached the end of the program, so the program ends.

Examples of built-in operators include:
  * `+` - add two numbers
  * `/` - divide two numbers
  * `array.push` - push a value onto the end of an array
  * `exit`  - exit the running program

## Code examples
Let's take a look at a few code examples. Since Gnarly isn't a real language (yet), syntax highlighting on GitHub is a bit limited. `bash` highlighting has been used as an approximation.


### Hello world
```bash
# Simple hello world example

# `print` operator takes 1 operand and prints it to stdout
# Prints "Hello, world!"
"Hello, world!" print

# The string concatenation operator `+` can be used to print more things at once
# Prints "Hello, world!"
"Hello, " "world!" + print

# Or, possibly, using `array.join`
# `array.join` takes 2 operands: an array, and a character sequence
# It then formats each array element into a string separated
# by the character sequence
# Prints "Hello, world! 1 2 3"
["Hello," "world!" "1" "2" "3"] " " array.join print
```

### Variables and operators

Gnarly is a strongly typed language despite being interpreted. It is designed such that it _could_ be compiled, and it's been demonstrated time and time again (e.g. TypeScript) that a typed language with ahead-of-time (AOT) static analysis makes a language far easier to work with.


```bash
# Declare a variable called `$two` of type `number` with
#   default value `2` (a number literal)
# Attempting to declare it to a non-number type would
#   produce an interpreter error
# All declarations in gnarly require a default value,
#   there are no implicit declarations
number $two 2 declare
number $three 3 declare

# Perform $two + $three, then store the result in $result
number $result 0 declare
$two $three + $result set

# Alternatively, you can immediately initialise a variable
#   to the result of an expression
number $result2 $two $three + declare

# Subtract $two from $three, then print the result
$three $two - print

# Exit the application with code 0
0 exit
```

### Working with arrays

Arrays are strongly typed, as they are just variables like anything else. Array literals can be declared by specifying each value separated by a whitespace character.

```bash
# Declare a `number` array called `$array`, initialised to `[1 2 3]`
number[] $array [1 2 3] declare
# `array.push_all` pushes all of the values in its first
#   operand (an array) to the end of the array specified by
#   the last operand, $array
[4 5 6] $array array.push_all

# An array's length can be fetched by referencing `.length` on an array variable
# Prints "6"
$array.length print

# Similarly, the members of an array can be indexed through
#   familiar `[]` syntax. The result of the inner expression
#   is used as the index. Arrays are 0-indexed.
# Prints "2"
$array[1] print
# Prints "6" (2 + 3 = 5)
$array[2 3 +] print

# Negative numbers are accepted as offsets from the end
# Prints "6"
$array[-1] print

# When converted to strings, arrays are formatted by separating
#   each value with `, ` by default
# Of course, `array.join` could be used explicitly instead
# Prints "Array: 1, 2, 3, 4, 5, 6"
"Array: " $array + print

# `array.pop` removes the last element from the end of the array
$array array.pop

# Prints "Array: 1, 2, 3, 4, 5"
"Array: " $array + print
```

### Custom operators

In Gnarly, you can define custom operators, akin to functions or subroutines in other languages. If you really want, you can even override an existing operator. When declaring an operator, you declare its name, what operands it takes, and its result type (if any). When your operator has finished processing, it can put values back on the stack or simply stop processing using the `return` keyword.

Unlike other postfix languages, Gnarly does not use (abuse) the stack to pass operands to operators. They are instead explicitly defined as variables (which live in an ambient context). In fact, each operator context is given its own completely empty stack. The operator has no access to the stack from the calling context. When the operator has finished processing, its stack is discarded. The `return` keyword is used to push a value from the local stack to the calling context's stack. This makes operators in Gnarly very similar to function calls in regular languages like C.

```bash
# Define a custom operator called `square`
%square define_operator
  # Declare 1 argument for this operator, called `$num`
  number $num arg
  # Result is of type `number`
  # Result is pushed onto the stack after returning
  number result
begin
  # Perform $num multiplied by $num and push the result back onto the stack.
  # The `return` operator pops the top value off the current stack and pushes
  #   it back onto the stack of the calling context.
  $num $num * return
end

# The result from `square` is pushed onto the stack by the `return` statement
#   which is then consumed by `print`.
# Prints "9"
3 square print

# Create an `add1` operator by currying the `+` operator
%add1 define_operator
  number $a arg
  number result
begin
  1 $a + return
end_operator

# Unsurprisingly, prints "11"
10 add1 print
```

### Loops

Gnarly contains several different keywords for looping in different ways. Several common forms of looping are supported:


```bash
# Count from 1 to 10 using the variable `$i`
for 1 10 $i do
  "i: " $i + print
end

# Iterating an array
number[] $array [1 2 3 4 5] declare
# Print each item of an array using variable `$item`
for_each $array $item do
  "Item: " $item + print
end

# While loop
# A while loop uses only a simple construction so
#   it can be used for whatever other custom form of
#   iteration is desired
# A while loop just takes any boolean value as its condition
number $x 0 declare
# Count 0-5 in 0.5 increments
while $x 5 lte do
  # Print the value of $x
  "x: " $x + print
  # Add 0.5 to $x
  $x 0.5 + $x set
end
```


### Merge sort

Here is a more comprehensive example of some "real world" programming, an implementation of [merge sort](https://en.wikipedia.org/wiki/Merge_sort). The implementation below uses an in-place sort, it modifies the array directly, using `array.swap`.

```bash
%merge_sort define_operator
  # The start index of the range to sort
  number $start_index arg
  # The end index of the range to sort (inclusive)
  number $end_index arg
  # The array to sort (in-place)
  number[] $array arg
begin
  # Calculate the length of the current range
  number $length $end_index $start_index - 1 + declare

  if $length 1 lte then
    # Range is just 1 (or 0) value(s) - it is sorted
    return
  elseif $length 2 eq then
    # Range is just 2 values. Test if these are in order, or if they need to be swapped
    if $array[$start_index] $array[$start_index 1 +] gt then
      # Swap 2 values if they are out of order
      $start_index $start_index 1 + $array array.swap
    end
    return
  else
    # Range is more than 2 values
    # Calculate the midpoint (pivot) of the array
    number $pivot $length 2 / math.floor $start_index + declare

    # Merge sort the left and right halves
    $start_index $pivot 1 - $array merge_sort
    $pivot $end_index $array merge_sort

    # Fold the two halves together, in place
    # Maintain 2 pointers that refer to each half of the array
    #   while it's being folded
    number $left_pointer $start_index declare
    number $right_pointer $pivot declare
    while $left_pointer $end_index lt $right_pointer $end_index lte and do
      if $array[$left_pointer] $array[$right_pointer] gt then
        # Number at left pointer is greater than number at right pointer
        number $swap_pointer $right_pointer declare

        # Perform pairwise swaps until number at right pointer
        #   is to the left of left pointer
        while $swap_pointer $left_pointer gt do
          $swap_pointer $swap_pointer 1 - $array array.swap
          $swap_pointer 1 - $swap_pointer set
        end

        # Update right pointer to next index
        $right_pointer 1 + $right_pointer set
      end

      # Update left pointer to next index
      $left_pointer 1 + $left_pointer set
    end
  end
end

# Declare a number array with some values
number[] $arr [1 5 2 10 6] declare

# Sort the whole array (i.e. from `0` to `length-1`)
0 $arr.length 1 - $arr merge_sort

# Prints "1, 2, 5, 6, 10"
$arr print
```

## Backlog

  * Document standard lib, grammar, and built-in operators
