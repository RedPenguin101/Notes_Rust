# Basic stuff

## Cargo commands
* `cargo build`
* `cargo run`
* `cargo test`
* `cargo new myapplication --bin`

## misc
* end expressions with ;
* create scopes with {} blocks
* comments are `//`

## variable declaration
* `let x = 5`
* `let x: u32 = 5` the 'u32' is the type annotation
* variable naming snake case
* `let mut x = 5`

### shadowing
* use _shadowing_ when you want a variable to be modifiable for a few lines but long term immutable: 
* `let x = 5; let x = x + 1`, the second expression _shadows_ the first
* a shadowing variable can change the type of the variable it shadows

### constants
* `const X: u32 = 5` 
* constants can't be mutable, and must be annotated. 
* convention is to capitalise. 
* available any time in the scope they are in
* can be in global scope
* since they are set at compiletime they can't be set to an expression result

## Data types
* scalars: integers, flp, booleans, characters
* integers are u8, u16, u32, u64, u128, usize, for unsigned, and the same with i for signed
* flp are f32 and f64, just use f64. type is inferred from dp `let x = 2.0`
* numeric operators: +, -, *, /, %
* boolean: true, false.
* chars: type inferred with single quotes. Double quotes inferred as string literals

### compound types
* tuple `let tup: (i32, f64, u8) = (600, 5.3, 1);`
* fixed size but can have different types
* destructure: `let (x, y, z) = tup
* index with dot `x.0` is 0th element
* array are fixed size and have same type
* `let a: [i32;5] = [1,2,3,4,5]`
* alternative initiator: `a = [3;5]` a length five element of 3s
* index with `a[8]`

## functions
```rust
fn my_func(x: u32) -> u32{
  println!("hello world! Parameter is {}", x);
  x * 3
}
```
* paramters must be type annotated
* if there's a return value, that must be type annotated
* you can return early with `return`


### statement and expression
* statements are instructions that perform an action and don't result in / return a value
* expression evaluate to a resulting value. Expressions don't end in ;. adding that turns it to a statement
* a function is a set of statements optionally ending in an expression
* `let y = 6` is a statement, it doesn't return. so you can't do `let x = (let y = 6)`
* expressions: `6+5`. calling a function or a macro. a scope {} block

## Control flow
### if
```rust
if number < 6 {
  println!("thing1");
} else if number < 8 {
  println!("thing2");
} else {
  println!("thing3");
}
```
* condition must be explicit `bool`, there's no 'truthy' evaluations in rust
* ifs can be used in let statements: `let number = if condition {5} else {6};`. 
* type is inferred from the 1st arm, all arms myst return same type to compile

### loops
* `loop {println!{"I am an infinite loop")}`
* break a loop with `break`. can have a return value from break with `break my_return_expression`
* while loops with `while`
* `for element in iterable {...}`
* use array with `.iter()` or `Range` with `1..4` (this is included..excluded)
* reverse your iterable with `.rev()`
