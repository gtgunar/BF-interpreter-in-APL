# Brainfuck-interpreter-in-APL

This is an interpreter for the Brainfuck Programming language, written in APL. It is a function definition that takes the Instruction Tape as function input and takes standard I/O as Runtime I/O.
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

BFinAPL takes the Instruction Tape as a string, as it's righthand input. 
The function body consists of an inner function, between curly braces, and the calculation of it's arguments.

the left argument is calculated in "(0,⍨  '+-<>.,[]'  ⍳⍵)". This term, as it often happens, is evaluated basicly from right to left, as the functions on the left need the calculation of their input first. First ⍵( Omega ) is evaluated, which is just the functions original input, in this case, the instruction Tape. Then ⍳( Iota ) is called, whith the inputs of ⍵ and the string that contains the valid symbols of Brainfuck. ⍳ Substitutes* the symbols on the right with the index of their appearance in the string on the left. This means, every "+" becomes 1 (I'm using one indexed arrays here, but it can be set to 0), every "<" becomes 3, and every missing symbol becomes 9. This subsitution is purely to improve readability, as these numbers are only needed for comparisons later. Finally, with a ",", a 0 is concatenated* to the whole string. "0,..." would append on the left, but the ⍨ ( Switch ) Operator is used. It is a higher order function, and it swaps the inputs of the function it is applied to. Higher order functions take either 2 arguments, or one on the left, and have a higher precedent than first order functions. Finally, the parentheses are used to calculate it all before passing it to the inner function. 

On the right side the ⍴( Reshape ) function takes 30000 and 0, resulting in the 30000 element long vector of 0-s.

When the arguments are calculated, the inner function can be called. It starts with a branch "0≠⊃⍺:". This is denoted by the ":". If the term on the left is true, do the term on the right. There is also an option for an else branch, which is separated by ⋄ ( Diamond ). Since there is none here, the falseness of the starting term exits the function. "0≠⊃⍺" checks whether the first element, of left argument, denoted by ⍺( Alpha ), is not 0. The first element is accessed by ⊃ ( First ).

The inner function is recursive, wtih ∇ ( Nabla ) being the entry point of the recursion. ⍺(F∇G)⍵ is a type of term called a fork. When 3 functions with 2 arguments are written next to each other, in a single place, and evaluated as a single unit( ensured by the parentheses ). The evaluation happens by feeding the arguments obtained from outside the parentheses, in this case the outer inputs, to the right and left functions, which are evaluated, and then their outcome, to the middle function. Forks also work if the two functions on the side are single varable. 

The function F takes the crruent Data and Instruction Tape and calculates the next state of the instruction Tape. It consists of a branching, with an else included. The deciding term is "(1⍳⍨7 8=⊃⍺)  =  1+×⊃⍵". The term in parentheses, first checks whether the first of alpha equals to 7 or 8. These would mean angled brackets on the original Tape. The outcome of the check is a 2 element vector of bools. then it finds the 1, the true in the vector, giving 1 for opening bracket, 2 for angled bracket, and 3 for something else. Then it evaluatesd the term "1+×⊃⍵". It accesses the first of the right argument,which is ther Data Tape then calls ×( Sign ) to get it's sign. It is -1 for negative, 0 for 0 and 1 for positive numbers. Then it adds one, so the final values are 0 1 2 respectively. Finally it evaluates the equality of the left and the right term. They are equal if the symbol is an opening bracket, and the Data Tape's first is 0, or if the symbol is a closing bracket and the head of the Data Tape is positive. Otherwise it's false.

If it is true, it evaluates "⍺⌽⍨0⍳⍨+\-⌿7 8∘.=⍺". The first function in it, to be evaluated is "∘.=". It is made of the ∘( Join ) and the "."( Inner Product ) operators, with equality. The term "∘.h" is the idiom for outer product, where "h" is a 2 argument function. Outer product takes all possible pairs of of it's left and right arguments and evaluates h with them*. In this case what it does is checks whether the elements of the left argument are 7 or maybe 8, and writes it into a 2×n matrix, where n is the length of the left argument. The upper row contains whether it's 7 the lower if it is 8. After this, an other higher order function is called, ⌿( Reduce First ) with substraction bound to it. When dealing with tensors( vectors, matrices, or even higher order blocks), there is a hierarchy among the axis. Matrices are considered a column vector made of exual length of row vectors for example. Reduce first, goes trough the first axis, which is for a matrix the columns. It carries out the diadic operations in a chain with the output being the left argument of the next input until it get's trough the whole tensor.* In this case it's simply subtracting the second row, from the first, which ends up with a vector where 7 is replaced by 1 and 8 by -1. After this, there is the "\"( Scan ) oparot, with + as the function bound to it. It works similarly, to Reduce first, with 2 differences. It' does not return only the last outcome of the whole chain of calculations, but the whole process, with the partial solutions making up the vector. And it acts along the last axis.* This carried sum essentially goes from left to right and  adds the next element and writes down the outcome in it's place. With 7 and 8 denoting the brackets, this summing denotes the depth of symbols in the brackets. When entering a block at a 7, the first 0 denotes the point of exitinmg the bracket block. That 0 is searched with ⍳ as the next step. Once it is found, the whole vector of the instruction tape is rotated by the 0's position, essentially rotating to the exit point to the bracket.*

If the condition at the start of F evaluates to false, it simply rotates to the next element on the instruction tape.

Thge function G takes the Instruction and Data Tapes and returns the next state of the Data Tape. It is a chain of functions which are in practice evaluated from right to left.

"(-/1 2=⊃⍺)+(@1)⍵" uses the @ ( At ) operator, which takes some identifier on the right and a function, and an input vector. It applies the function on an index, if the identifier indicates it. It can be a simple number to denote an index, or a logical expression that returns true on some indicies. The conditionally applied function may take input on it's left. In this case "+(@1)" adds the term to the left, onto the frst element of the vector on the right, which is the original input, the Data Tape. The number added to it is calculated, by checking if the Head of the instruction Tape is 1, maybe 2. This returns 2 booleans, "1 0" if it's 1, "0 1" if it's 2, and "0 0" otherwise, not adding anything, if it's not  + or - on the original instructions. finally, the second is subtracted from the first, giving +1 for "+" and -1 for "-". It is then added to the Head of the Data Tape. 

Next "(-/3 4=⊃⍺)⌽" is applied, which checks for 3, 4 originally denoting "<" and ">", and roteates by the subtraction's outcome similar to the previous term. A rotation by 0 is a Noop. 

After this, "({⎕(@1)⍵}⍣(6=⊃⍺))" is executed. The ⍣( Power ) operator, in this context is applied to run a function either 0 or 1 times. It takes a function on the left, and a number on the right, and applies the function as many times as the number tells. The argument taken initially is to the right of the entire term. Here it is used as an alternative notation of branching, as applying a function 0 times is a Noop, and boolean terms are simply 0 or 1. The term on the right whether the head of the Instruction Tape is 6, originally a ",". If it is, it calls ⎕ ( Quad ) on the first of the input. Again, if the condition fails, it is simply a Noop. 

After this, "({⎕←⊃⍵⋄⍵}⍣(5=⊃⍺))" is exectued, this time, checking for 5, "." instructing a print. The conditionally executed function is "{⎕←⊃⍵⋄⍵}". Quad works as a symbol for IO. When it is read from, it means reading from standard I/O, when it is written into, it denotes writing into standard I/O. After wrting, the Diamond is used as a statement separator, and the a simply Omega is called, implicitly returning, the now modified input of the function.*

Finally, since the cells are meant to be single byte, basicly being integers from 0 to 255, it is being ensured by taking the modulo of the Data Tape with 256. Thge order of modulo is not of "%" from conventional programming languages, but of mathematical notation. Becaulse of rank polymorphism as scalar taking term being applied to a vector, it is applied to every element of the Data Tape.

* It is actually a function that can take either 1 or 2 arguments. in it's 1 arguemnt form, it takes a whole number, and generates all the integers up to it (from 0 on 0 indexing 1 for 1 indexing).
* Differentiating concatenation and appending doesen't really make sense becaulse of rank polymorphism. Essentially, single values can be considered one element vectors, or even 1×1 matrices, or even higher order tensors, and vectors can be considered paralell collection of elements, so for example "1 2 3 + 2"=3 4 5.
* Outer product with concatenation is the Cartesian Product.
*  It is similar to the fold function in other languages, but it has no initial accumulator element. Such functionality can be accessed, by appending the initial accumulator, to the tensor, on the beginning.
*  In this case with a 1 dimensional vector it does not make any difference, but using "/"( Reduce ) for the difference would have not worked. Also ⍀( Scan First ) exists as well.
*  When looking at closing brackets ( 8 ) it finds 0, which denotes the element after the corresponding opening bracket. It works becaulse the Tape is rotated circularly, and the closing bracket gets the sum to start as negative 1.
*  APL Permits and supports imperative code as well, some dialects even support OOP, and more conventional control flow macros. 
