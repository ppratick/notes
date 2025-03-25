## Built-in Comparison Operators
- Operators: `>`, `<`, `>=`, `<=`
- Operate on: `int`, `string`, `char`, `real`
## Totality and Valuability
- **Definition (Valuable).** A well-typed expression `e` is valuable if there exists a value `v` such that `e ⟹ v`. We also say that e evaluates to a value.
- **Definition (Total).** A well-typed expression `e1 : t1 -> t2`, for types `t1` and `t2`, is total for all values `e2 : t1`, the expression `e1 e2` is valuable.
- You have to cite totality when you are stepping through a function and there is function in it's argument.
- You don't have to cite totality when you use the IH, Math, or a Lemma. 
## Pattern Matching
- **Pattern matching** is attempting to match an evaluated expression against a pattern. There are five types of patterns: variables, tuples, constructors, constants, and wildcards.
- Used in lambdas, `case`, `val`, and `fun`.
  ```sml
  (case f of
     Vanilla => "Vanilla"
   | Chocolate => "Chocolate"
   | Strawberry => "Strawberry")
  ```
## Induction 
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
## Operators
- **Numeric:** (`* : int & real`, `/ : real`, `div : int`, `mod : int`, `+ : int & real`, `- : int & real`):
- **String (`^`):** `"foo" ^ "bar" = "foobar"`.
- **List (`::`, `@`):** `1 :: [2,3,4] = [1,2,3,4]`, `[1,2] @ [3,4] = [1,2,3,4]`.
- **Associativity:** Most left-associative. Right: `::`, `@`, `->`.
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
## Polymorphism
**(a)** There are no values of type `' a`.
**(b)** There are expressions of type `' a`. (Ex. `raise ____`)
**(c)** There are values of type `'a list`. (Ex. `[]`)
**(d)** There are function values of type `'a -> 'b` (Ex. `fun loop x = loop x`).
**(e)** There are no total function values of type `' a -> 'b`
## Hofs
```sml
(* 1. map : ('a -> 'b) -> 'a list -> 'b list *)
(* Applies a function to each element of a list and returns a new list with the results *)
fun map f [] = []
  | map f (x::xs) = (f x) :: (map f xs)
(* 2. filter : ('a -> bool) -> 'a list -> 'a list *)
(* Returns a new list containing only the elements that satisfy a given predicate *)
fun filter p [] = []
  | filter p (x::xs) = if p x then x :: (filter p xs) else (filter p xs)
(* 3. foldr : ('a * 'b -> 'b) -> 'b -> 'a list -> 'b *)
(* Combines elements of a list from right to left using a function and a base value *)
fun foldr f z [] = z
  | foldr f z (x::xs) = f (x, foldr f z xs)
(* 4. foldl : ('a * 'b -> 'b) -> 'b -> 'a list -> 'b *)
(* Combines elements of a list from left to right using a function and a base value *)
fun foldl f z [] = z
  | foldl f z (x::xs) = foldl f (f (x, z)) xs
(* 5. o : ('b -> 'c) * ('a -> 'b) -> ('a -> 'c) *)
(* Function composition; combines two functions into one *)
infix o
fun f o g = fn x => f (g x)
```

```sml
fun rev L = foldL (op ::) [] L  (* NOT Point-Free Programming *)
val rev = foldL (op ::) []      (* Point-Free Programming *)
```
## CPS
```sml
(* search : ('a -> bool) -> 'a tree -> ('a -> 'b) -> (unit -> 'b) -> 'b *)
fun search p Empty sc fc = fc ()
  | search p (Node(l, x, r)) sc fc = 
      if p(x) then sc x  
      else search p l sc (fn () => search p r sc fc)
```

```sml
(* fibCPS: int -> (int -> 'a) -> 'a *)
fun fibCPS 0 k = k(0)
  | fibCPS 1 k = k(1)
  | fibCPS n k = fibCPS (n-1) (fn res1 => fibCPS (n-2) (fn res2 => k(res1 + res2)))
```

![[IMG_5221.png | 400]]
![[IMG_3301.png | 400]]
## Exceptions 
### Example 1 (Does NOT Work)
```sml
exception Poly of 'a
```
- **Reason:** You cannot bind a type variable directly in an exception declaration.
- Type variables must already be in scope for polymorphism to work.
### Example 2 (Works)
```sml
fun f (x : 'a) : unit =
	let 
		exception Poly of 'a
	in 
		()
	end
```
- **Reason:** `'a` is already in scope due to the function parameter.

```sml
e1 handle ⟨exn name⟩ => e2
```
* If e1 and e2 have type t, so does e1 handle Foo => e2
* If e1 evaluates to v, so does e1 handle Foo => e2
* if e1 raises Foo, e1 handle Foo => e2 ==> e2
* If e1 raises Bar, so does e1 handle Foo => e2
* If e1 loops, so does e1 handle Foo => e2

```sml
e handle <exn1> => e1 | <exn2> => e2 | ... 
(* e, e1, and e2 must have the same type *)
(* handle isn't a function it's a keyword *)
```

**4 built in exns**
```sml 
exception Div 
exception Match
exception Match
exception Fail of string
```

Note: If you handle an undeclared exception, handle treats it as a wildcard. You can only handle an exn. 
![[IMG_6035.png | 400]]![[Screenshot 2025-03-24 at 8.24.21 PM.png | 400]]
## Regex
```sml
datatype regexp = Zero
                | One
                | Char of char
                | Plus of regexp * regexp
                | Times of regexp * regexp
                | Star of regexp
```
This defines a datatype regexp with the following constructors:
- Zero – Represents the empty language.
- One – Represents the language consisting of just the empty string.
- Char c – Represents the language consisting of the one-character string "c".
- Plus (a, b) – Represents the union of two languages.
- Times (a, b) – Represents the concatenation of two languages.
- Star a – Represents the repetition of a language.
**Matching Semantics**
1. Zero matches nothing.
2. One matches the empty string "".
3. Char c matches exactly one character c.
4. Plus (a, b) matches if either a or b matches.
5. Times (a, b) matches if a followed by b matches.
6. Star a matches zero or more repetitions of a.
```sml
fun match (Char a) cs k = 
		(case cs of [] => false 
		    | c::cs' => (a=c) andalso (k cs')) 
  | match Zero _ _ = false 
  | match One cs k = k cs 
  | match (Plus(r1,r2)) cs k = (match r1 cs k) orelse (match r2 cs k) 
  | match (Times(r1,r2)) cs k = match r1 cs (fn cs' => match r2 cs' k) 
  | match (Star r) cs k = k cs orelse 
						  match r cs (fn cs' => match (Star r) cs' k) 
  
fun accept r s = match r (String.explode s) List.null
```

```sml
val REJECT : matcher = fn _ => fn _ => false
val ACCEPT : matcher = fn cs => fn k => k (cs)
val CHECK_FOR (a : char) : matcher = 
		fn [] => REJECT
		 | c::cs => if (a = c)
					then ACCEPT cs
					else REJECT cs
fun (m1 ORELSE m2) cs k = m1 cs k orelse m2 cs k
fun (m1 THEN m2) cs k = m1 cs (fn cs' => m2 cs' k)
fun REPEAT m cs k = 
		let 
			fun mstar cs' = kEs' orelse 
							m cs' (fn cs => properSuffix (cs'', cs') andalso
											mstar cs'') 
		in 
			mstar cs 
		end
```

```sml
fun match (char(a) : regexp) : matcher = CHECK_FOR a
  | match (Zero) = REJECT
  | match (One) = ACCEPT
  | match (Plus (r1, r2)) = match r1 ORELSE match r2
  | match (Times (r1, r2)) = match r1 THEN match r2
  | match (Star r) = REPEAT (match r)
```
