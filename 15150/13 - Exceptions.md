### Defining Exceptions
Exceptions can be defined in SML using the `exception` keyword.
#### Example:
```sml
exception SegFault
exception Fail of string
```
- `SegFault` defines a simple exception.
- `Fail` defines an exception that carries a `string` value.

---

## Raising Exceptions
Use the `raise` keyword to signal an exception.

#### Example:
```sml
raise Fail "Testing out raise"
```
Produces:
```sml

uncaught exception Fail [Fail: Testing out raise]

```

---

## Handling Exceptions
Exceptions can be handled using the `handle` keyword.
```sml
e handle <exn1> => e1
       | <exn2> => e2
       | ...
```
#### Example:
```sml
fun safeDiv x y = (if y = 0 then raise Div
				  else x div y) handle Div => 0
```

---
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

---  
# SML Exception Examples and Types

## Examples
1. `Fail` – Raises the `Fail` exception.  
2. `Fail "hmm"` – Raises the `Fail` exception with the message `"hmm"`.  
3. `raise Eyebrows` – Raises the `Eyebrows` exception.  
4. `raise (case (raise Eyebrows) of _ => Suspicion)` – Raises the `Suspicion` exception after pattern matching.  
5. `(raise Fail "I can't") handle it => it` – Catches the `Fail` exception and returns the message.  
6. `let fun l e = raise e in (1 o 1 o 1) Suspicion end` – Defines a function `l` that raises `e`.  
7. `case (raise Eyebrows, raise Suspicion) of (1, 2) => 3 | _ => 5` – Pattern matching on exceptions.  
8. `case (raise Eyebrows) of 1 => 2 | "three" => 3` – Type error due to mismatched patterns.  
9. `(if raise Eyebrows then raise Suspicion else 1.0) handle Eyebrows => 2.0` – Catches the `Eyebrows` exception and returns `2.0`.  
10. `(raise Suspicion, raise Eyebrows) handle Suspicion => 3 | _ => 4` – Pattern matching on exception tuples.  
11. `(raise Suspicion, raise Eyebrows) handle _ => ("cat", 3)` – Returns `("cat", 3)` after catching the exception.  
---
## Type Solutions
1. **Type:** `string -> exn` – Evaluates to the value `Fail` (constructors are values).  
2. **Type:** `exn` – Evaluates to the value `Fail "hmm"`.  
3. **Type:** `'a` – Raises exception `Eyebrows`.  
4. **Type:** `'a` – Raises exception `Eyebrows`.  
5. **Type:** `exn` – Evaluates to the value `Fail "I can't"`.  
6. **Type:** `'a` – Raises exception `Suspicion`.  
7. **Type:** `int` – Raises exception `Eyebrows`.  
8. **Not well-typed** – Patterns differ in type, so `raise Eyebrows` can’t be assigned a type.  
9. **Type:** `real` – Evaluates to the value `2.0`.  
10. **Not well-typed** – The type of the expression being handled is `'a * 'b`, but the type of the handle clauses are `int`.  
11. **Type:** `string * int` – Evaluates to the value `("cat", 3)`.  