# Brainfuck-interpreter-in-APL

This is an interpreter for the Brainfuck Programming language, written in APL. It is a function definition that takes the instzruction tape as function input and takes standard I/O as Runtime I/O.
It is not a codegolfed version, but a more human readable one. 

# How it Works
The main body is BFinAPL, with two auxillary functions, F and G.

F←
  {
  (1⍳⍨7 8=⊃⍺)  =  1+×⊃⍵ : 
    ⍺⌽⍨0⍳⍨+\-⌿7 8∘.=⍺
  ⋄
    1⌽⍺
  }

G←
  {
  256|
  ({⎕←⊃⍵⋄⍵}⍣(5=⊃⍺))
  ({⎕(@1)⍵}⍣(6=⊃⍺))
  (-/3 4=⊃⍺)⌽
  (-/1 2=⊃⍺)+(@1)⍵
  }

BFinAPL←
{
(0,⍨  '+-<>.,[]'  ⍳⍵)
  {
  0≠⊃⍺:
    ⍺(F∇G)⍵
  }
30000⍴0
}

BFinAPL takes the instruction tape as a string, as it's righthand input. 
The function body consists of an inner function, between curly braces, and the calculation of it's arguments.

the left argument is calculated in "(0,⍨  '+-<>.,[]'  ⍳⍵)". This term, as it often happens, is evaluated basicly from right to left, as the functions on the left need the calculation of their input first. First ⍵( Omega ) is evaluated, which is just the functions original input, in this case, the instruction tape. Then ⍳( Iota ) is called, whith the inputs of ⍵ and the string that contains the valid symbols of Brainfuck. ⍳ Substitutes* the symbols on the right with the index of their appearance in the string on the left. This means, every "+" becomes 1 (I'm using one indexed arrays here, but it can be set to 0), every "<" becomes 3, and every missing symbol becomes 9. This subsitution is purely to improve readability, as these numbers are only needed for comparisons later. Finally, with a ",", a 0 is concatenated** to the whole string. "0,..." would append on the left, but the ⍨ ( Switch ) Operator is used. It is a higher order function, and it swaps the inputs of the function it is applied to. Higher order functions take either 2 arguemnts, or one on the left, and have a higher precedent than first order functions. Finally, the parentheses are used to calculate it all before passing it to the inner function. 

On the right side the ⍴( Reshape ) function takes 30000 and 0, resulting in the 30000 element long vector of 0-s.

When the arguments are calculated, the inner function can be called. It starts with a branch "0≠⊃⍺:". This is denoted by the ":". If the term on the left is true, do the term on the right. There is also an option for an else branch, which is separated by ⋄ ( Diamond ). Since there is none here, the falseness of the starting terrm exits the function. "0≠⊃⍺" checks whether the left argument, denoted by ⍺( Alpha )
It is a recursive function wtih ∇ ( Nabla ) being the entry point of the recursion.





* It is actually a function that can take either 1 or 2 arguments. in it's 1 arguemnt form, it takes a whole number, and generates all the integers up to it (from 0 on 0 indexing 1 for 1 indexing).
