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
* debug print with `"{:?}"` and pretty print with `"{:#?}"`

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

## Range Syntax
* a Range object has syntax `x..y` where x and y are integers
* when slicing `..3` is the range from the first (0th) index to the 2nd inclusive. `3..` goes to the last. `..` is the whole thing

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

### slices
* a contiguous sequence of elements from a collection
* denoted with &, with range syntax in square bracks
* `let s = String::from("hello world"); let hello = &s[0..5];` - hello here is a sting slice type, denoted `&str`
* a string literal is actually a string slice
* you can also have arrays of other collections, like arrays. Array slices are like `&[u32]`

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

### Match
* pattern matching: compare a value against patterns and execute on the one that matches
* like an if without the boolean condition restriction 
* arm syntax is `pattern => exec_code`

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}
```

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

### borrowing and references
* If you want to keep the original variable after passing it into a function or other scope, you pass a _reference_ to the object with `&`, and the object will be moved into the function, then moved back at the end. this is __borrowing__


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

### mutable references
* By default, an object can't change something it borrows. If you want the function to modify, you have to pass a _mutable reference_ with `&mut s` (the object you're passing itself has to be declared mutable too)
* you can only have 1 mutable reference to an object in scope at any one time (to prevent data races)

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

### dangling pointers 

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


## Structures
* a `struct` is a data structure - fields but no methods
* define like with comma separated, type annotated field names
* instantiate like a dictionary KV pair (note if you want fields in the struct to be mutable you need the whole object to be)
* access with dot notation `user1.email`
* use a constructor method / function to instantiate with default values - by naming your params to match the fields you can shorthand
* a structure with no fields is called a _unit like structure_
* add derived traits by decorating: `#[derive(Debug)]` allows you you `println!("{:?}", my_struct)`
* __tuple structs__ are structs whose fields have no key `struct Color(i32, i32, i32); let black = Color(0,0,0);`

### definition
```rust
struct User {
  username: String,
  email: String,
  sign_in_count: u64,
  active: bool,
}
```

### instantiation
```rust
let mut user1 = User {
  email: String::from("uname@mail.com"),
  sign_in_count: 1,
  active: true,
  username: String::from("uname"),
}
```

### build with shorthand
```rust
fn build_user(email: String, username: String) -> User {
 User {
    email,
    username,
    sign_in_count: 1,
    active: true,
  }
}
```

## Methods
* use `impl` (implementation) block
* must always pass `self` as first arg, meaning the object the method is being called on - normal rules about referencing and mutability apply. reference to the calling structs fields are `self.field`
* __Associated Functions__ are functions that are defined in the `impl` block but don't act on an instance of a struct (i.e. they are not passed self as the first arg.). Class methods basically. often used for contructors. Access them with namespace notation `::`

### Associated Function 
```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}

let sq = Rectangle::square(3);
```

## Enums
* aka enumerations. define a type by specifying its possible values.
* define like `enum IpAddrKind { V4, V6 }`
* create instance like `let four = IpAddrKind::V4` - note variants are namespaced under identifier
* It can be useful to have the variants be structs or tuple structs, which are defined inline
* you can still implement methods on enums

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

### Option enum vs Nulls
* an enum in the std lib (though you don't need to bring it in scope, it's always there)
* the scenario where you are something or you are nothing - rusts answer to null
* it has variants `Some` and `None` - don't need to namespace them here, so they are basically keywords
* In effect it means you don't have to null check everything. because you can't add an `i32` and `Option<i32>` and only the Option can be 'null', you never accidentally try to add a non-null and null.
* when you DO have a Option, you generally want to explicitly handle the cases where that option is None and Some - usually with match 

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```
