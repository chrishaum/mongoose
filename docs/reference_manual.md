# Mongoose Reference Manual

Group 17  
Mark Fischer (mlf2156)  
Chris Haueter (cmh2122)  
Michael Rubin (mnr2114)  
Bo Xu (bx2104)  
Etan Zapinsky (eez2107)  

## Introduction

This manual describes in detail the Mongoose Programming Language, a language designed to easily facilitate creating independent-agent, discrete-event simulations. This manual serves as the standard for anyone who would like to program in Mongoose. The interpreter is built with the syntactic conventions described in this document.

The manual will start by discussing the structure of a Mongoose program and how those components execute in a well defined order. Then the document will delve into the lexical conventions, the meaning of identifiers, casting, expressions and objects. Leading to how these components interact, using declarations, initialization, statements, definitions and scope. Finally, it will conclude with a summary of the grammar of the language.

## The Four Blocks

Each Mongoose program consists of four main blocks of code: the environment, the agent(s), the termination conditions, and the analysis block. The environment is mainly responsible for holding global variables, and for populating itself with the agents initially involved in the simulation. It also has an action function it can perform every tick of the clock. 

Agents are objects that hold state, have functions that enable them to be created or destroyed, and have an action that is performed at particular time periods, “simultaneously” with the action of every other agent. Simultaneously is defined as follows: agents have actions that occur at certain time intervals; the intervals are defined within the action. All actions that occur within a single time step occur simultaneously; in other words, the action takes into account only the status quo at the beginning of the time step. This is analogous to transactions: each individual actor's action is executed within a time step, but only once all of the actions have completed (and the various environmental invariants been enforced), is the transaction completed.

Terminating conditions are checked at the end of time periods, the frequency and order of which can be specified by the programmer. If any of these invariants proves to be false, the simulation ends and an action can be performed, before the final analysis is done. The analysis block is carried out at the end of the simulation, after it terminates.

## Order of Execution

When a Mongoose program is run, first the interpreter determines the scope of the functions and variables declared in the file; they are either at the global scope of the file, the scope of a block, or at the scope of a function within a block. This allows the Mongoose interpreter to effectively determine if a block is using variables that it has access to. 

After determining scope, the next thing that occurs is that the environment’s instance variables are instantiated with a call to its populate() function. At the same time, every time an agent is created its instance variables are instantiated with a call to its create() function. Then, until the program terminates, the following things are performed in order: first, the agents’ action() functions are all carried out simultaneously (if they are acting on the given turn), that is they all act based on the same shared state. Next, the environment’s action() is invoked, but it may depend on new state as changed by the agents’ actions. Next, the termination conditions are checked in the order they are listed, and if any of them fail, additional actions may be called before the simulation is brought to a halt. Finally however, if none of them fail, the TICKCOUNT increments by 1 and the cycle repeats. After the simulation finally terminates, the additional analysis block of code is executed.


## Lexical Conventions

### Lexical Conventions
A program consists of one body of source code, stored in a single file. After being processed by the lexer, this program is represented as a sequence of tokens.

### Comments
In Mongoose, comments are denoted by the # symbol. They start at the symbol and end when there is a new line. Comments are not tokens; they are ignored by the lexer.

### Tokens
The language has the following classes of tokens: identifiers, string literals, keywords, operators and other separators (brackets, commas, etc.). White space is considered significant when it separates tokens, but is otherwise ignored.

### Identifiers
An identifier is a sequence of digits, letters and underscores that does not begin with a digit. Identifiers may be of arbitrary length, and all characters in the identifier are significant.

### Keywords
The following identifiers are keywords. They are reserved by the language and may not be used in any other way.

and
elif
pelse
return
float
none
analysis
or
else
then
in
string
agent
terminate
not
pif
for
range
obj
true
environment
if
pelif
while
int
bool
false
self
pass
env

### Strings
In Mongoose, strings can be denoted by either ‘single quotes’ or “double quotes.”  A string is a primitive type in Mongoose. Mongoose allows escaped characters \n (newline), \t (tab), \” (double quote), \’ (single quote) and \\ (backslash). No string formatting is supported naturally in Mongoose.

## Meaning of Identifiers

### Basic Types
The basic types that Mongoose supports are signed ints and floating point numbers, booleans and strings. Note that ints and floats are only signed and not unsigned. The size of these primitive types is platform-specific.#

### Collection Types
Mongoose has one primary collection type and that is the list. This is synonymous to the Python list, but the types it contains (boolean, int, float, list and object) have to be declared in the list declaration. Lists are identified by placing [] after the type that will be contained in the list. They are defined as follows: int[] or float[].

One important note is that there can be multi-dimensional lists, i.e. a list of lists, etc, but a list can only contain either another list or the base type of the list. For example, int[][] or float[][][].

In Mongoose, lists have a public length field that holds the list’s length.

### Functions

Functions in Mongoose are very similar to those in Java or C. They are defined as follows:
return_type function_name() {expression}
Since Mongoose is a pass-by-reference language, parameters to functions get passed by reference, except for primitives which get passed by value. Mongoose supports anonymous functions in certain contexts, which are defined as: {expression}.

### Agent Definitions

An agent definition in Mongoose is a blueprint for creating agents, similar to classes acting as blueprints for objects in other object oriented languages. For the purpose of agent modeling, agent definitions allow us to efficiently create agents and behaviors associated with the simulation.
none
Similar to Python, Mongoose has a none type, signifying a null, non-existent option.

### Built-in Values

TICKCOUNT is a built-in int value representing the current time step of the simulation. 
Casting
To have a functioning program, there is a need to be able to easily convert between int and float, from int, float and boolean to string and from int, float and string to boolean. 

When converting an int to a float no information is lost, however, converting a float to a string will discard the fractional part. Then when using the concatenation operator + between a string and a float or int, the entire integer part of the number will be represented up to 10 digits after the decimal if the number is a float. When converting to a boolean from an int or float, all non-zero values are true and only zero is false, and when converting to a boolean from a string all non-empty strings are true and only the empty string is false. Similarly, only empty lists are cast to boolean false.

## Expressions

### Math
    # arithmetic precedence and associativity is defined using regular
    # mathematical rules
    mathematical-expression :
    mathematical-expression + mathematical-expression
    mathematical-expression - mathematical-expression
    mathematical-expression * mathematical-expression
    mathematical-expression / mathematical-expression
    mathematical-expression % mathematical-expression
    mathematical-expression ^ mathematical-expression
    
Basic mathematical operations are supported like addition, subtraction, multiplication, modulus, division and exponentiation.

### Dot Operator
To reference an object’s variables and functions the dot operator ‘.’ is used. For example, object_name.variable_name would access variable_name and object_name.function_name would access function_name.

### List Referencing 
To reference an element of a list, the notation list_name[i] is used, where i is an integral index of the list.

### Function Calls
Function are of the form function()or with parameters function([type] a, [type] b, ...). The dot operator allows for function calls to be of the form an_agent.function(), as scoping is explicit in Mongoose.

### Unary Operators
Mongoose uses unary operators to denote unary minus, and to denote logical negation. Unary operators associate right-to-left. The unary minus symbol is the - operator, and the logical negation symbol is the not operator.

### Casting
To convert between primitive types, explicit casting is allowed, and done in a way similar to how Java or C does so. To convert an expression of type A to one of type B, one would use the expression:
(B) A

On top of casting explicitly from one type to the next there is also implicit casting performed as well. For instance, if a float was assigned to an int, the float would be converted to an int and then stored in the int, likewise the same would occur if assigning an int to a float, and similarly for other types that can be implicitly cast in Mongoose. 

### Relational
The relational operators in Mongoose are the following infix operators, all of which return a boolean value: < (less than), > (greater than), <= (less than or equal), >= (greater than or equal). If the result is true, then the boolean value true is returned. If not, then false is returned.

### Equality
The language has the == (is equal to) and != (is not equal to) equality infix operators. There is no implicit casting when using these operators; the programmer is responsible for using these operators only with variables of the same type. A runtime error occurs if equality operators are used with variables of different types. Primitives and lists are compared by value, and agent types are compared by reference.

### Logical Operators
    boolean-expression :
        boolean-expression and boolean-expression
    boolean-expression or boolean-expression
    not boolean-expression
    [true|false]

To operate on booleans the operators and, or and not are used. The operators and and or are left associative with and having a higher precedence over or,and not is right associative, similar to the unary minus. Also, these operators can be chained arbitrarily.

### Conditional Operators
The conditional operators are or signifying logical OR, and and signifying logical AND. 

### Assignment Operator
The assignment operator in Mongoose is represented by the = symbol. The left-hand-side variable is assigned the value on the right-hand-side, assuming the types are compatible. The assignment operator does not return a reference in Mongoose.

### Comma Operator
Commas are the symbol , used to separate variables. This is used mainly in dealing with functions with parameters.

### Probabilistic Value
A probabilistic value in Mongoose is an expression such as the following: 
(3:true | 7:false)
This is a pipe-separated, parenthesized list of colon-separated weight-value pairs wi,vi, where each wi is a positive integer. The wi are normalized to calculate the probability related to each value. The result of this expression is probabilistic; for example, with the expression above, the expression will be true with 3/10 likelihood and false with 7/10 likelihood (wi is the numerator and Σ wi (for all i) is the denominator of the probability).

As another  example, one may assign the value of a die-roll as such:
int die = (1: 1 | 1: 2 | 1: 3 | 1: 4 | 1: 5 | 1: 6)
If a chosen vi is not of the same type as the left-hand-side variable, it will be implicitly cast, if possible. If not, a runtime error will be thrown.

A weight wi must be provided for each each value to use this construct; otherwise a syntax error will be thrown.

### Terminating Expression
Terminating expressions provide a convenient syntax for defining expressions that are run with some certain frequency, and which, if they evaluate to true, first execute some function, then end the simulation. They provide a nice syntax for testing termination conditions of the simulation, while allowing the programmer to limit the frequency of the tests, in order to avoid slowing the simulation through excessive condition testing.

For example, the terminating expression <pos_int>:(expr) {foo()} is syntactically equivalent to if ((TICKCOUNT % <pos_int> == 0) and (expr)) then {foo()}, where <pos_int> is either a non-negative int, or a function returning a non-negative int. If such a function returns a negative int, a runtime error occurs. 

## Objects

### self Keyword
An important part of an object-oriented language is the ability to reference an object from inside itself: that’s where the self keyword comes in. Like the self keyword in Python, in Mongoose using self is necessary for the object to access its own member functions and variables.

### env Keyword
All of the environment’s instance variables are readable by any part of a Mongoose program. When using one of these variables, the programmer must prepend that variable with the keyword env and then the dot operator.

### Agents
Agents are objects that hold state, have functions that enable them to be created or destroyed, and have an action that is performed at particular time periods, simultaneously with the action of every other agent being performed.

### Agent Actions
Actions are functions, belonging to agents, that are executed only at particular times defined by the programmer. Their declarations share the <pos-int>: func() {...}  syntax of terminating expressions, where <pos-int> is an expression or function yielding a non-negative integer; the integer indicates the number of timesteps that will occur before the function is next executed. The <pos-int>: portion is optional here, unlike in terminating conditions where the : is required. The <pos-int> expression is evaluated before the action itself is evaluated, and it does not have access to data within the scope of the action function. All action() functions return none.

### Environment
Environments encompass a representation of the setting that agents reside in. Examples include 2D “boards” with rows and columns. The environment allows for the querying of relationships between agents and keeps the state of all the agents. 

### Block Contents
Blocks can contain variables, or state, as well as functions. Agents and the environment must contain an action() function, which is called at specified time periods at the appropriate point in the execution cycle. Agents must contain a create() and a destroy() method, while environments must contain a populate() method.

### Invariants
Invariants are expressions that are checked at the end of time periods, at a frequency and in an order determined by the user. When one of the invariants, also known as terminating conditions, evaluates to true, the simulation terminates and performs some final statements.

## Declarations

### Type Specifiers
Since Mongoose is a statically typed language, objects and primitives are declared with their type preceding them, as in Java or C. Types can be either user-defined agent types, lists, or one of the primitive types available in Mongoose: int, float, bool, string or none.

### Default Values
The default values for various types are as follows: none for objects, the empty string for strings, 0 for ints, 0.0 for floats, and false for booleans.

### Agent/Environment Declaration
Only agents can have objects of their type instantiated by the programmer. Agents and environments are declared by defining them as such:
agent AgentName() {...}
environment {...}
Note that the first letter of AgentName()is capitalized; this is a requirement, and without it a lexer error will be emitted. Mongoose is opinionated about the naming of its agent definitions.

### List Declaration
Lists are declared with the type of the list, followed by one or more square brackets. For example, bool[][] declares a two-dimensional array of type bool.

### Function Declaration
Functions in Mongoose are declared like so:
type function() {code block}

## Initialization

In Mongoose, objects and lists must be declared and initialized before their values can be accessed. This can occur with separate declaration and initialization expressions (e.g. int a followed by a = 3), or in a combined initialization expression (e.g. int a = 3).

### Objects and Agent Classes
An agent is initialized to an object by calling the agent class name as if you were calling a function, e.g. agent an_agent = AgentClassName(). When the agent is initialized there is an implicit call to the create() function of the applicable agent class to set up the necessary internal state of the instance. Then when an object is killed, there is another implicit call to the destroy() function of the agent class to perform any last operations before being deleted. Objects are also killed by program termination. Mongoose is opinionated about agent class names. They must start with a capital letter, which is followed only by letters and underscores.

### Kill
When one wishes to destroy an agent, one simply calls the built-in kill() function on the agent’s reference, and its destroy() function is invoked.

### List
Mongoose lists are dynamic, so they do not require initialization — they may be resized dynamically at runtime. Once declared, however, lists may optionally be initialized in two ways: either with defaults for the list type, or with a list literal. For example, after its declaration as int[] a, the identifier a may be initialized with the default for its type (zero, in the case of the int type): a = int[2][2] initializes a with [[0,0],[0,0]]. Alternatively, it may be initialized with a list literal: a = [[1,2],[3,4]]. Finally, the declaration may be combined into one expression with either initialization expression, such as int[][] a = int[1][1] or int[] a = [1,2,3]. These initializations are optional; in other words it is possible simply to declare a list and then begin appending to it and accessing its elements. A runtime error occurs if the program attempts to access a list element that does not exist (i.e. when the list is empty).

## Statements

### Statements
There are a couple of different types of statements:
statement :
expression
selection-statement
prob-selection-statement
iteration-statement
jump-statement
assignment-statement
block-statement
Individual statements are terminated by the newline (\n) character; a statement list must contain at least one statement. The pass keyword is provided for cases in which a statement list must fulfil this requirement without actually doing anything; this keyword is a statement, but does nothing.

### Expressions
Mongoose provides for boolean, mathematical, cast and object-access expressions. Expressions may or may not be parenthesized.

### Selection: if/elif/else
The selection statement evaluates an expression and, based on the boolean value of the expression, executes another statement. The statement is executed in if(expr) {statement} only if expr evaluates to true. The optional (one or more) elif (short for else-if) clauses, of form elif(expr) {statement}, are executed if the preceding if-expression and elif-expressions all evaluated as false. The optional else clause (of form  else {statement}) comes last, and executes if neither the if, nor the elif expressions evaluated as true.

### Probabilistic Selection: pif/pelif/pelse
Randomness is a very important aspect of simulations. As such, Mongoose includes an easy syntax for probabilistic selection, which mimics Python’s if()-elif()-else statements of deterministic selection. A probabilistic selection statement contains one pif() block at the beginning, may end with a pelse block, and can zero or more pelif() blocks in between. At most one of the blocks will ever execute. 
Like an if or elif command, each pif and pelif contains an expression in parentheses that determines whether the block executes or not. However, these expressions are positive floating point probabilities, which must sum to 1 in each probabilistic selection statement if no pelse block is involved, and must sum to a number less than or equal to 1 if a pelse block is involved. For example, in the code below, 
pif(0.3) {
A()
}
pelif(0.5) {
    B()
}
pelse {
    C()
}
A() will be called with 0.3 probability, B() will be called with 0.5 probability, and C() will be called with 1 - 0.3 - 0.5 = 0.2 probability. Not adhering to these conventions regarding probabilities summing to 1 will result in a runtime error.

### Iteration: for Loops
For-loops in Mongoose take the form for (<type> var in list_of_<type>) {statement-list}. Thus, iteration occurs primarily across lists. A range() built-in library function is provided that yields a list of sequential integers within a given range; this function allows C-style iteration.

### Iteration: while Loops
The Mongoose while loop is identical to the Python while loop (i.e. while (expr) {stat}). The expr is evaluated at the beginning of each iteration, and the {stat} block is executed only if expr evaluates to true.

### Return
Returning from a function in Mongoose is similar to returning from a function in C or Java. The function evaluates to the returned value, and the program continues from where it left off before the function was called.

## Scope

The environment’s and agents’ variables and functions at the uppermost level have global visibility and can be accessed as long as a reference to the agent is available (A reference to the environment env is always available). Variables inside of functions, terminate and analysis blocks, and other statement blocks have scope limited to their block. 

### Scope of Functions

Functions can be defined in any of the 4 blocks of a Mongoose program, or outside of any of the 4 blocks. A function within a terminate or analysis block can only be called from within that block. The functions in the environment or agents, such as populate(), create(), destroy() or any user-defined functions there can be called from anywhere, via dot notation on the reference to an agent. Note that action() functions may only be called by the Mongoose interpreter. Functions defined outside any of the 4 types of blocks can be called from any of the 4 blocks as well.

# Appendix A: Complete Grammar
statement :
expression
selection-statement
prob-selection-statement
iteration-statement
jump-statement
assignment-statement
block-statement
function-definition
function-call
*

expression :
    ( expression )
    boolean-expression
    mathematical-expression
    cast-expression
    agent-access-expression
relational-expression

selection-statement :
if (boolean-expression) { statement-list } if (boolean-expression) { statement-list } else {statement-list}
if (boolean-expression) { statement-list } opt-elif else { statement-list } 
    
opt-elif :
    elif ( boolean-expression ) { statement-list } opt-elif
    \epsilon

prob-selection-statement :
pif (float-expression) { statement-list } pif (float-expression) { statement-list } pelse {statement-list}
pif (float-expression) { statement-list } opt-pelif pelse { statement-list } 
    
opt-pelif :
    pelif ( float-expression ) { statement-list } opt-pelif
    \epsilon

iteration-statement :
    for ( declaration-statement in list ) { statement-list }

list :
    [ type-list ]

type-list :
    type , type-list
    type
\epsilon

jump-statement :
    return expression

assignment-statement :
    identifier = expression

block-statement :
    block-name { statement-list }
    { statement-list }

block-name : 
[agent|environment|analysis|terminate]

function-call : 
    identifier ( parameter-list )
    { statement-list }

boolean-expression :
    boolean-expression and boolean-expression
boolean-expression or boolean-expression
not boolean-expression
[true|false]

# arithmetic precedence and associativity are defined using standard
# mathematical conventions
mathematical-expression :
mathematical-expression + mathematical-expression
mathematical-expression - mathematical-expression
mathematical-expression * mathematical-expression
mathematical-expression / mathematical-expression
mathematical-expression % mathematical-expression
mathematical-expression ^ mathematical-expression
( mathematical-expression )
- mathematical-expression
int
float

int :
    [0-9] int-suffix
    - [0-9] int-suffix

pos-int: 
    [0-9] int-suffix

int-suffix :
    [0-9] int-suffix
    epsilon

float :
    . int
    int .
    int . int

cast-expression :
    ( type ) identifier

agent-access-expression :
    identifier . identifier

relational-expression :
    mathematical-expression < mathematical-expression
    mathematical-expression <= mathematical-expression
mathematical-expression > mathematical-expression
mathematical-expression >= mathematical-expression

agent-instantiation :
    agent identifier = agent-identifier ( parameter-list )

identifier :
    [_A-Za-z] identifier-suffix
    
identifier-suffix :
    [_A-Za-z0-9] identifier-suffix
    \epsilon

agent-identifier :
    [A-Z] agent-identifier-suffix
    
agent-identifier-suffix :
    [_A-Za-z] agent-identifier-suffix
    \epsilon

declaration-statement :
    complex-type identifier
    complex-type assignment-statement

list-declaration :
    type list-constructor

list-constructor :
    [ ] list-constructor
[ ]

function-definition :
    complex-type identifier ( parameter-list ) { statement-list }
    pos-int : complex-type identifier ( parameter-list )  { 
statement-list }

parameter-list:
    complex-type identifier , parameter-list
complex-type identifier
\epsilon

statement-list :
statement \n statement-list
statement \n
epsilon
pass

complex-type :
    type
    list-declaration

type :
[int|float|string|bool|none]
identifier

terminating-condition :
    optional-pos-int-colon ( boolean-expression ) function-call

action-def :
    none optional-pos-int-colon action ( parameter-list ) { 
statement-list }

optional-pos-int-colon :
    pos-int :
    function-call :
    \epsilon