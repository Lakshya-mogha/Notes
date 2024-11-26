``` c
#include <stdio.h>
main(){
printf("hello, world\n");
}

```

The main function marks as the start of the program and can be used to call other functions.

The first line of the above program tells the compiler to include information about the standard input/output libraries.

In unix systems you can compile the .c file with command :
```
cc hello.c
```
it will create a.out file which can be run to see the output.

- ### variables and arithmetic expressions 
  
  In C , all the variables must be declared before they are used, usually at the beginning of the function before any executable statements.
  
  The range of both int and float depends on machine that is used; 
  16-bit int, lie between -32768 and +32768, are common as are 32-bit int. 
  Float are typically 32-bit with at least 6 significant digits and magnitude generally between 
  $10^-38$ and $10^+38$  
  
  | data type |                                  |
  | --------- | -------------------------------- |
  | char      | character - a single byte        |
  | short     | short integer                    |
  | long      | long integer                     |
  | double    | double-precision floating point  |
  The size of these objects are also machine dependent. 
  there are also arrays, structures and unions of these basic types, pointers to them, and functions that return them.
- #### printf
  printf is a general-purpose output formatting function,
  
  ```c
  printf("%d\t%d\n", fahr, celsius );
  ```
  
  Its first argument is a string of characters to be printed, with each % indicating  where one of the other(second, third, .......) argument is to be substituted and in what form it is to be printed. For instance %d specifies an integer argument. They must match up properly by number and type, or you'll get a wrong answer.
  
  ```\t``` is used for printing tab spaces.
  ```\n``` is used for printing new line.
  
  
  > [!NOTE] printf is not a part of C lang; 
  > There are no input or output  defined in C itself. printf is a useful function for standard library that are normally accessible in C.
- ### Loops
- #### while loop
  
  ```c
  while (i<j) {
  i = 2 * i;
  }
  /* Or */
  
  while (i<j) i = 2 * i;
  
  ```
- #### For loop
  
  ```c
  for (int i = 0; i<=10; i++){
  printf("%d", 2 * i);
  }
  ```
  
  For loop is a generalisation of while loop.