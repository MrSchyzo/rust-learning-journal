# Rust learning journal

---

## Day 1


### Road to `cargo`less `hello world`

#### Install `rustup`:
```bash
url --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

#### Open offline documentation after `rustup` installation:
```bash
rustup doc
```

#### Install some plugins on VSCode

The list is:
- [`tamasfe.even-better-toml`](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml)
- [`matklad.rust-analyzer`](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer)
- [`serayuzgur.crates`](https://marketplace.visualstudio.com/items?itemName=serayuzgur.crates)


#### Compile single `.rs` file
```bash
rustc file.rs
```


### Road to `cargo`ful `hello world`

#### New `cargo` project
```bash
cargo new <project_name> && cd <project_name>
```

#### Build `cargo` project (compiles to `target` subdirectory)
```bash
cargo build
```

#### Build and run `cargo` project
```bash
cargo run
```

#### Check `cargo` project code without compilation (faster than `cargo build`)
```bash
cargo check
```


### Road to first `cargo` toy project

#### New project
```bash
cargo new guessing_game && cd guessing_game
```

#### Code a bit (follow chapter 2)

---

## Day 2


### Road to debugging

#### Install LLDB
```bash
sudo apt update && sudo apt install lldb
```

#### Install CodeLLDB for VSCode
Click [here](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb)

### Chapter 3.1 notes

#### Mutability
Variables can be declared as `mut`able or not.
```rust
let x = 4;     //Can't be assigned again
let mut y = 6; //Can be assigned again
```

#### Constant vs immutable variables
1. Constant can only be set to a constant expression (no function calls or other runtime computations)
2. Type _must_ be specified in constants
3. (Naming convention) constants are named `LIKE_THIS` while variables are named `like_this`

#### Shadowing
1. You can re`let` variables in the same block:
    ```rust
    let x = 0;
    let x = x + 1;
    let x = x - 3;
    // Now you have a stack with 3 different variables called "x"
    // Latter variables shadow the former ones
    // Variables with the same name do not have to share the same type
    ```
2. You can mix shadowing with `mut`able and un`mut`able declarations:
   ```rust
   let x = 0;
   let mut x = x + 1;
   let mut x = x.to_string();
   ```

### Chapter 3.2 notes

#### About types
1. Rust is statically type and tries to infer variables' type
2. You can still annotate a variable with its expected type
   ```rust
   let value: u32 = 5;
   ```

#### Scalar types
1. Integers: 
   - signed integers (`i<bits>`) and unsigned integers (`u<bits>`);
   - you can have 8, 16, 32, 64, 128 bits integers; 
   - there is also a `isize`/`usize` type of integers, depending on runtime architecture (e.g. 32 and 64 bits);
   - signed integers uses "two's complement" representation;
   - visual digit separator `_` (e.g. you can write `1_000_000`)
   - literals can have type as suffix (e.g. `128u8` or `1024i16`)
   - different literal integers formats:
     - decimal (regex `[0-9]+`): `10_000`
     - hex (regex `0x[0-9a-f]+`): `0xcafe_babe`
     - octal (regex `0o[0-7]+`): `0o777_000`
     - binary (regex `0b[01]+`): `0b1111_0000`
     - byte (as unsigned 8bit integer, regex `b'[\x00-\x7f]'`): `b'!'`
   - overflow can occur: non-release executable `panic` while release executables don't; **do not** rely on overflow wrapping unless you use these methods:
     - `wrapping_<add|multiply|etc.>` to explicitly wrap overflows;
     - `checked_<add|multiply|etc.>` that returns an `Option<...>` (`None` when an overflow occurs);
     - `overflowing_<add|multiply|etc.>` that returns a `(..., bool)` (`(<result>, true)` when an overflow occurs);
     - `saturating_<add|multiply|etc.>` to restrict a result within its natural boundaries (e.g. `254u8.saturating_add(2u8)` returns `255`);
2. Floating-point:
   - two types: `f32` (single precision) and `f64` (double precision);
3. Numeric operations:
   - good ol' infix operators (they can be `overload`able, more on that later);
   - you cannot add different numeric types without `as <num_type>` casting or explicit rounding;
4. `bool`, `true/false`
5. `char`:
   - it is a 4-byte value that can contain any Unicode character;
   - ranges are: `U+0000...U+D7FF` and `U+E000...U+10FFFF`;
   - more on characters and string later;
6. `unit` type, or `()`:
   - single value that holds no semantics, used to highlight the non-importance of a result;
   - its only value is assignable and can be evaluated from expressions (more on that later).

#### Compound types
1. Tuples:
   - type is annotated like `(type1, type2, ..., typeN)`;
   - "unary" tuples are equivalent to the only type of the tuple (`(i32)` is `i32`);
   - you can deconstruct tuples:
     ```rust
     let tup = (1u16, 0u8, '⅝');
     let (x, y, c) = tup; //assigns n-th value to n-th symbol
     ```  
   - you can retrieve any value inside a tuple (0-base indexing):
     ```rust
     let tup = (1u16, 0u8, '⅝');
     let x = tup.0; //first value of the tuple
     let y = tup.1; //second value of the tuple
     ley c = tup.2; //third value of the tuple
     ```  
2. Arrays:
   - collection of fixed length with uniform type;
   - allocated in the stack;
   - array types are annotated like this: `[type; num_items]`
   - you can initialize an array with the same values like this:
     ```rust
     let a = [0; 100]; //a is an [i32;100] with all its values set to 0
     ``` 
   - classic 0-based index access: `a[0]`;
   - rust `panic`s if an out-of-bounds access is executed;

### Chapter 3.3 notes

#### Basic stuff:
1. `main()` is the entrypoint for an executable;
2. `snake_case` as convention for functions;
3. `parameters` are the symbols associated with the function input, while `arguments` are the effective values passed to the function in a certain call;
4. you declare a function like this:
   ```rust
   fn name_of_function(name_1: type_1, ..., name_n: type_n) -> return_type {
       //body
       return something; //body can also end with an expression evaluation
   }
   ```
5. return type annotation can be omitted if the function does not have any return statement or does not end with an expression evaluation; in that case, return type annotation `-> ()` is assumed.

#### Expressions vs statements
Expressions are evaluated, while statements are not. This means that expressions yield values that can be used for assignment or function/method calls.

1. Statements:
   - variable declarations;
   - function declarations;
   - any expression evaluation followed by `;`;
   - (looks like) they always have to end with `;`
2. Expressions:
   - (almost) anything else;
   - `if`s are expressions as well (`if`s without `else` return `()`);
   - honorable mention, block (scope) expression:
     ```rust
     let z = 0i128;
     // It can be a sequence of statements that end with an expression evaluation
     // If this last evaluation is followed by `;` the resulting value of the block will be ()
     // You cannot have non-statement evaluations in the middle of the block
     let k = {
         let w = z as u8;
         !w
     };
     ```

### Chapter 3.4 notes
Nothing really useful, except for the fact that comments are single-line only.

### Chapter 3.5 notes

#### `if` expressions
- These kind of expressions accepts only booleans in the condition part and other types are not implicitly converted in boolean values. The condition must be an expression that yields `bool`.
- Branches must yield the same type: evaluating `if` must have a single type and this means that you cannot do stuff like `let x = if <cond> {5} else {"string"};`.
- This also means that you can also inline an `if` expression as a condition for another `if` expression (FTLOG, please don't do it).
- You can cascade `else if` branches like in other languages.

#### `loop` expression
- `loop` expects can be exited only with a `break` statement;
- this `break` statement returns a value to the `loop` expression;
- this means you can do something like this:
  ```rust
  let mut i = 10u8;
  let loop_returns: u8 = loop {
      i -= 1;
      if i < 1 {
          break i;
      }
  }
  ``` 

#### `while` expression
It is a common `while`, so:
- it has an exit condition;
- you can `continue` and `break` it;
- can only be evaluated as `()`;

#### `for...in...` expression
It is a `for` that accepts values that implement the `Iterator` Trait:
- you can `continue` and `break` it;
- can only be evaluated as `()`;


---


## Day 3 - into ownership concepts

### Chapter 4.1 notes
Illustrating ownership:
- each value has an owner;
- an owner is a variable that holds the ownership;
- ownership of a value is held by one owner at a time;
- when the owner goes out of scope, its owned values are dropped;
- in heap terms, dropping a value means `free`ing its occupied memory region (Rust automatically calls `drop` method of the freed value).

#### The `String` type as an ownership "study case"
- `String` is a type stored into the heap and does not represent string literals.
- To get a `String` out from a literal, you can `String::from("literal string")`.
- Since `String` instances reside in the heap, they can be mutated:
    ```rust
    let mut string = String::from("World");
    string.push_str(", hello!");
    ```
- Assigning heap references means `mov`ing the value if its type does not implement the `Copy` trait.
  - This is also true when you pass those references in function calls.
- `mov`ing the value means invalidating the old variable, making it unusable (it avoids the "double free problem").
- In order to make the old variable usable, a new assignment can be done (if the variable is `mut`able, of course).

#### Ownership transfer
Ownership transfers occurs for non-Copy types in these cases:
1. assignment
   ```rust
   let string1 = String::from("Hello world");
   let string2 = string1; //<-- value of string1 has been moved to string2. Ownership transferred.
   println!("{}", string1); //Does not compile because string1 value has been borrowed by string2: string1 old reference is not valid anymore.
   ```
2. function calls (beware: functions, not macros!)
   ```rust
   let string1 = String::from("Hello world");
   foo(string1); //<-- value of string1 has been moved to function call frame. Ownership transferred.
   println!("{}", string1); //Does not compile because string1 value has been moved to function call: string1 old reference is not valid anymore.
   ```
3. return values
   ```rust
   fn foo() -> String {
     let r = String::from("hello");
     r //<-- value of r will be moved outside the function, so it will be not freed after the function scope
   }
   fn main() {
     let s = foo(); //<-- value returned is owned by s
     println!("{}", s);
   }
   ```
4. function call + return value
   ```rust
   fn foo(s: String) -> String { //<-- value is owned by s
     println!("{}", s);
     s //<-- value of s will be moved outside the function, so it will be not freed after the function scope
   }
   fn main() {
     let s = String::new("hello");
     let s1 = foo(s); //<-- ownership is transferred at call-time but it's returned by the function body, no "free"s yet
     println!("{}", s1); //<-- it wouldn't compile if s was used instead
   }
   ```

### Chapter 4.2 notes

#### References
- passing reference to function is called "borrowing";
- used to refer a value without taking its ownership;
- it makes it work stuff like this:
  ```rust
  fn strlen(s: &String) -> usize { //strlen(s: String) would take ownership of argument, rendering it unusable after the call
      s.len()
  }

  fn main() {
      let s1 = String::from("hello");
      let len = strlen(&s1); //Ownership is not transferred, thus s1 is not moved 
      println("String '{}' is {} chars long", s1, len); //<-- s1 is still usable
  }
  ```
- you can have mutable references too:
  ```rust
  fn change(s: &mut String) { 
      s.push_str("hello"); //<-- push_str is a method that needs self mutable reference, hence we need a &mut String, and not a &String
  }
  
  fn main() {
      let mut s1 = String::from("Hello"); //<-- needs to be mutable too
      change(&mut s1); //<-- passing a mutable reference to s1
  }
  ```
- multiple mutable borrows render previous mutable references unusable:
  ```rust
  fn main() {
      let mut s1 = String::from("Hello");
      let ptr = &mut s1;
      println!("{}", ptr);
      let ptr1 = &mut s1; //<-- this will be the row that will be highlighted for borrow errors
      println!("{}", ptr1);
      println!("{}", ptr); //<-- This will not compile because this ptr has not the latest borrow in this scope
  }
  ```
- cannot have mutable and immutable references in the same scope:
  ```rust
  fn main() {
      let mut s1 = String::from("Hello");
      let ptr = &s1;
      let ptr1 = &s1;
      println!("{} {}", ptr, ptr1);
      let ptr2 = &mut s1; //<-- this will be the row that will be highlighted for borrow errors
      println!("{}", ptr2);
      println!("{} {}", ptr, ptr1); //<-- This will not compile because this ptr and ptr1 are invalidated by the mutable reference
  }
  ```
- specify lifetime when returning references of values that will go out of scope after the end of the block:
  ```rust
  fn dangle() -> &String { //Won't compile without specifying a lifetime: it would return a pointer to a freed memory region
      let s = String::from("Hello");
      &s
  } //s goes out of scope and it owns the string, hence its memory region is freed
  ```

### Chapter 4.3 notes

#### Slices and String type
Reference of a part of a contiguous region in the memory (heap only?).
Example: 
```rust
let s = String::from("Hello World");
// WARNING: slice refers bytes: non-ASCII chars are longer than 1 byte!
let hello = &s[0..5];  //Readonly reference restricted to bytes 0,1,2,3,4
let world = &s[6..11]; //Readonly reference restricted to bytes 6,7,8,9,10
```
"Generic" syntax for a string slice expression is: `&<identifier>[<int_range>]` and `<int_range>`:
- is end-exclusive: `[m..n] == {m, m+1, ..., n-1}`;
- can omit start: `[..n] == {0, 1, ..., n-1}`;
- can omit end: `[m..] == {m, m+1, ..., <string_length>-1}`;
- can omit both: `[..] == {0, ..., <string_length>-1}` that returns a slice with the whole string in it;
