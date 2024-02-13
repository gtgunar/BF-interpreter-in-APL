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

When the arguments are calculated, the inner function can be called. It starts with a branch "0≠⊃⍺:". This is denoted by the ":". If the term on the left is true, do the term on the right. There is also an option for an else branch, which is separated by ⋄ ( Diamond ). Since there is none here, the falseness of the starting term exits the function. "0≠⊃⍺" checks whether the first element, of left argument, denoted by ⍺( Alpha ), is not 0. The first element is accessed by ⊃ ( First ).

The inner function is recursive, wtih ∇ ( Nabla ) being the entry point of the recursion. ⍺(F∇G)⍵ is a type of term called a fork. When 3 functions with 2 arguments are written next to each other, in a single place, and evaluated as a single unit( ensured by the parentheses ). The evaluation happens by feeding the arguments obtained from outside the parentheses, in this case the outer inputs, to the right and left functions, which are evaluated, and then their outcome, to the middle function. Forks also work if the two functions on the side are single varable. 

The function F consists of a branching, with an else included. The deciding term is "(1⍳⍨7 8=⊃⍺)  =  1+×⊃⍵". The term in parentheses, first checks whether the first of alpha equals to 7 or 8. These would mean angled brackets on the original tape. The outcome of the check is a 2 element vector of bools. then it finds the 1, the true in the vector, giving 1 for opening bracket, 2 for angled bracket, and 3 for something else. Then it evaluatesd the term "1+×⊃⍵". It accesses the first of the right argument,which is ther data tape then calls ×( Sign ) to get it's sign. It is -1 for negative, 0 for 0 and 1 for positive numbers. Then it adds one, so the final values are 0 1 2 respectively. Finally it evaluates the equality of the left and the right term. They are equal if the symbol is an opening bracket, and the datatape's first is 0, or if the symbol is a closing brtacket and the head of the data tape is positive. Otherwise it's false.

If it is true, it evaluates "⍺⌽⍨0⍳⍨+\-⌿7 8∘.=⍺". 




* It is actually a function that can take either 1 or 2 arguments. in it's 1 arguemnt form, it takes a whole number, and generates all the integers up to it (from 0 on 0 indexing 1 for 1 indexing).
**differentiating concatenation and appending doesen't really make sense becaulse of rank polymorphism. Essentially, single values can be considered one element vectors, or even 1×1 matrices, or even higher order tensors, and vectors can be considered paralell collection of elements, so for example "1 2 3 + 2"=3 4 5.
