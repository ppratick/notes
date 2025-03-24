# 15-150 Cheatsheet

## Type-Checking and Evaluation
- **Type-checking happens before evaluation!** Only well-typed expressions can be evaluated (into values).
- **Big idea:** The body of a function is not evaluated until we actually call the function with an argument
  - Example: `fn x => 1 div 0`
    This is still a value, `1 div 0` is not evaluated until we actually apply the function as in `(fn x   => 1 div 0) 5`
- What are the types of the following expressions (or NWT?)
  - `150::[]::[]`
    - NWT (no value)
  - `[15] @ [150]`
    - Type: int list
    - Value: [15, 150]

## Built-in Comparison Operators
- Operators: `>`, `<`, `>=`, `<=`
- Operate on: `int`, `string`, `char`, `real`

## Operations and Types
- A tuple of values is itself a value.
- () : unit, #"c" : char

## Binding Values
- **Big idea:** Bind values to variable names using `val` or `fun`.
  - **Examples:**
    ```sml
    val x : int -> int = (fn y => y + 1)
    fun x (y : int) : int = y + 1
    val add : int * int -> int = fn (x, y) => x + y  (* Merged from Function Binding *)
    fun add (x : int, y : int) : int = x + y         (* Merged from Function Binding *)
    ```
- **Note:** Declarations are not expressions. Do not use `val`/`fun` when an expression is required.

## Function Application
- **Big Idea:** `e e’ : t2` iff `e : t1 -> t2` and `e’ : t1`

## Totality and Valuability
- **Definition (Valuable).** A well-typed expression `e` is valuable if there exists a value `v` such that `e ⟹ v`. We also say that e evaluates to a value.
- **Definition (Total).** A well-typed expression `e1 : t1 -> t2`, for types `t1` and `t2`, is total for all values `e2 : t1`, the expression `e1 e2` is valuable.
- You have to cite totality when you are stepping through a function and there is function in it's argument.
- You don't have to cite totality when you use the IH, Math, or a Lemma. 

## Pattern Matching
- **Pattern matching** is attempting to match an evaluated expression against a pattern. There are five types of patterns: variables, tuples, constructors, constants, and wildcards.
- Used in lambdas, `case`, `val`, and `fun`.
- **Clause Syntax:** `pattern => expression`
  ```sml
  (case f of
     Vanilla => "Vanilla"
   | Chocolate => "Chocolate"
   | Strawberry => "Strawberry")
  ```

## Lambda Expressions
- **Syntax:** `fn pattern => expression`
- **Example:** `(fn (x : int, y : int) => x + y)` (Function value = Lambda Expression).

## Datatypes
- **Syntax:** 
  ```sml
  datatype <type_con> = <value_con_1> | ... | <value_con_k>
  ```
  Or, for a value constructor that takes in an argument of type t:
  ```sml
  datatype <type_con> = <value_con_1> of t
  ```
- **Example:**
  ```sml
  datatype flavor = Vanilla | Chocolate | Strawberry
  val bestFlavor : flavor = Chocolate

  datatype ingredient = Egg | Cheese | Ham
  datatype seasoning = Salt | Pepper
  datatype meal = Yum of ingredient * seasoning | Omelette of ingredient list

  Yum : ingredient * seasoning -> meal
  ```

## Induction and Recursion
- **Induction Process:**
  - State what type of induction you're using and on what
  - Base Case: Anything that doesn't have a recursive call
  - Inductive Case:
    - Weak/Strong : int = Let `n : int ≥ highest base case`   
    - List : int list = Let `L : list = (x::xs)` for some values `x : int, xs : int list`
    - Tree : tree = Let `T : tree = Node(L, x, R)` for some values `L : tree, x : int, R : tree`
  - Inductive Hypothesis:
    - Weak: Assume for some `n` : int such that `n ≥ lowest base case`, P(n) holds
    - Strong: Assume for all `k` : int such that `lowest base case ≤ k ≤ n`, `P(k)` holds
    - List: Assume for some `xs`, `P(xs)` holds
    - Tree: Assume for some `L` and `R`, `P(L)` and `P(R)` holds. 
  - Need To Show:
    - Weak: `P(n+1)` holds
    - Strong: `P(n+1)` holds
    - List: `P(x::xs)` holds
    - Tree: `P(Node(L, x, R))` holds
  - Showing: LHS = RHS

## Extensional Equivalence
- Two expressions are equivalent if they:
  1. Reduce to the same value.
  2. Raise the same exception.
  3. Loop forever.
- **Example:** `fn x => 2 + 2` ≈ `fn x => 4`.

## Operators
- **Numeric (`* : int & real`, `/ : real`, `div : int`, `mod : int`, `+ : int & real`, `- : int & real`):**
- **String (`^`):** `"foo" ^ "bar" = "foobar"`.
- **List (`::`, `@`):** `1 :: [2,3,4] = [1,2,3,4]`, `[1,2] @ [3,4] = [1,2,3,4]`.
- **Associativity:** Most left-associative. Right: `::`, `@`.

## Boolean Operations
- `andalso`, `orelse`, `not`.

## Work & Span
- **Work:** Worst case number of steps to sequentially evaluate code.
- **Span:** Worst case number of steps to evaluate code in parallel, given ∞ processors.

### Recurrences
- **Definition**: Equation describing complexity of recursive algorithm.

### Steps:
1. Write recurrence.
2. Find # levels, nodes at level i, work per node @ level i
3. Summation + Big-O bound.

### Tree Method:
$$
\sum_{i=0}^{\#\text{ levels}} (\text{nodes @ level } i) \times (\text{work per node @ level } i)
$$