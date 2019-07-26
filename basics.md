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
* `::` is used for specifying namespace. eg `String::from(literal)`

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

## Stack Data types
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
* arrays are over/underflow safe, they will throw errors at runtime if you try to access.

### string literals

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


### statement vs expression
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

## Ownership
* why? memory safety for the heap without auto garbage 
* everything you create which is put on the heap is owned by the variable (pointer) thet created it it and nothing else can change it. 
* when you pass it around (eg assignment, into a function or returning from a function) the ownership changes and the original variable gets dropped - this is __moving__ the heap data

```rust
// assigning and moving
let s1 = String::from("hello");
let s2 = s1;  // s1 is MOVED into s2

println!("{}", s2) // this is fine because s2 owns the heap memory location
println!("{}", s2) // this is not fine because s1 was moved to s2 - s1 can no longer be used
```
_this will fail at compiletime_

* when the scope of the variable ends (the `{}` block) it is dropped and the memory cleaned up. `}` has an implicit `drop` function - like an explicit RAII pattern in C++
* If you want to keep the original variable after passing it into a function or other scope, you pass a _reference_ to the object with `&`, and the object will be moved into the function, then moved back at the end. this is __borrowing__
* By default, an object can't change something it borrows. If you want the function to modify, you have to pass a _mutable reference_ with `&mut s` (the object you're passing itself has to be declared mutable too)
* you can only have 1 mutable reference to an object in scope at any one time (to prevent data races)

```rust
// simple borrow
fn main() {
  let s1 = String::from("hello");
  let len = calculate_length(&s1); // pass in a reference to the object s1
  println!("the length of {} is {}", s1, len"); 
  // we can still use s1 here because the object was borrowed by calculate_length and then ownership was transferred back to s1 
}

fn calculate_length(s: &String) -> usize { // expects a reference to a String, not a String
  s.len()
}
```
_note you have to pass in a reference when calling the function, and the function has to expect a reference, not an object_

```rust
// mutable referecnce
fn main() {
  let mut s1 = String::from("hello"); // the actual object must be mutable
  
  change(&mut s1); // you have to pass in a mutable reference
}

fn change(s: &mut String) { // expects a mutable reference
  s.push_str(", world");
}
```
```rust
// limited mutable reference
let mut s1 = String::from("hello");

let r1 = &mut s1; // OK
let r2 = &mut s1; // Not OK, 2 mutable borrows on s1 in same scope
```

* the opposite of referencing (`&`) is dereferencing (`*`)
* dangling pointers are caught at compile

```rust
// dangling pointer
fn main() {
  let ref_to_nothing = dangle();
}

fn dangle() -> &String {
  let s = String::from("hello"); // s will go out of scope...
  &s 
}                                // ...here
```
_this won't compile because it returns a reference to something which doesn't exist anymore_

## Heap data types
### String
* string literals are not `Strings`. SL are known at compile time and immutable
* `let s = String::from("hello")`.
* has a pointer, a length and a capacity
* use `let s2 = s1.clone()` to deep copy
