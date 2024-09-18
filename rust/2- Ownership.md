Ownership is Rust's most unique feature and has deep implications for the rest of the language. It enables rust to make memory safety guarantees without needing a garbage collector.

*** 

## What is ownership

ownership is a set of rules that govern how rust program manages memory.

 In rust memory is managed through a system of ownership with a set of rules that compiler checks. If any of the rules are violated ,the program won't compile. None of the ownership will slow down your program.

***

Notes on types of memory [[The stack and the heap]]

***
### Ownership rules

1. Each value in Rust has an owner.
2. There can only be one owner at a time.
3. When the owner goes out of scope, the value will be dropped.

***

```Rust
{                           // s is not valid here, since it's not yet declared 

	 let s = "hello";            // s is valid from this point forward 
	// do stuff with s

}                           // this scope is now over, and s is no longer valid
```

 -  When s comes into scope, it is valid.
 -  it remains valid until it goes out of scope.

### Using string laterals for ownership

 String laterals are convenient, but they aren't suitable for every situation in which we may want to use text.
 
 - One reason is that they are immutable.
 - Another is that not every string value can be known when we write our code  
 
 What if want to take user input and store it ? For these situations rust has a second string type , ```string```. 
 This type manages data allocated on the heap and as such is able to store an amount of text that is unknown to us at compile time.

```Rust
let s = String :: from ("hello");
```

*The double colon :: operator  allows us to namespace this particular from function under the  ```String```   type rather than using some sort of name like ``` string_from ```. This kind of string can be mutated.

```Rust
let mut s = String::from("hello");
s.push_str(", world!"); // push_str() appends a literal to a String
println!("{s}"); // this will print `hello, world!`
```

```output
hello,world!
```

So, what’s the difference here? Why can `String` be mutated but literals cannot? The difference is in how these two types deal with memory.

***

### Memory and allocation

In string literal the contents are directly hardcoded into the final executable at compile time. This is why sting laterals are fast and efficient.
Unfortunately, we can't put a blob of memory into the binary for each piece of text whose size is unknown at compile time or whose size might change at compile time.

with the  ```String```  type, in order to support a mutable text, we need to allocate an amount of memory on heap, unknown at compile time.

- The memory must be requested from the allocator at runtime.
- We need a way of returning this memory to the allocator when we're done with our ```String```.

For first part, When we call  ```String::from```, its implementation requests the memory it needs.

For second part, Rust takes a different path: the memory is automatically returned once the variable that owns it goes out of scope. 
When a variable goes out of scope, Rust calls a special function for us. This functions is called ```drop```, Rust calls  ```drop``` automatically at the closing curly bracket.

***

Behaviour of code can be unexpected in more complicated situations when we want to have multiple variables use the data we've allocated on the heap.

#### Variables and Data interacting with Move

Multiple variables can interact with the same data in different ways in Rust.

```Rust
let x = 5;
let y = x;
```

This snippet binds the value of 5 to x then make a copy of the value in x and bind it to y.

Let's take a look at String version:
``` Rust
let s1 = String::from("hello");
let s2 = s1;
```

This looks the same, so we might assume that the way it works would be the same.

![[Representation of string holdin a value.png]]
*Representation in memory of a* ```String``` *holding the value "hello" bound to s1*

The length is how much memory, in bytes the contents of ```String``` are currently using. The capacity is total amount of memory, in bytes, that is received from  the allocator.

when we assign s1 to s2, the String data is copied, meaning we copy the pointer, the length, and the capacity that are on the stack. we do not copy the data on the heap.

![[copy of s1 to s2.png]]


Earlier, Rust automatically calls the ```drop``` function and cleans the heap memory for that variable. But above figure shows that both data points at same location.

This is a problem, when they go out of scope both will try to free the same memory.
This is known as _double free error_ and is one of the memory safety bugs we mentioned previously. Which can lead to memory corruption, which can potentially become a security vulnerabilities.

To ensure memory safety, after the line ``` let s2 = s1;```, Rust consider s1 as no longer valid. Therefore, Rust doesn't need to free anything when s1 goes out of scope.

what happens when you try to use s1 after s2 is created.

```Rust
let s1 = String::from("hello");
let s2 = s1;

println!("{s1}, world!");
```

you'll get a error for using invalidated reference:

```output
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:5:28
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which
 does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |
5 |     println!("{s1}, world!");
  |                ^^ value borrowed here after move
```

In this example we would say ```s1``` was ```moved``` into ```s2```.

![[copy of s1 to s2.png]]

Rust will never automatically create “deep” copies of your data. Therefore, any _automatic_ copying can be assumed to be inexpensive in terms of runtime performance.


#### Variables and Data interacting with Clone
