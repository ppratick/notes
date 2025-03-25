 - **Continuation-passing style (CPS)** is a style of programming where you pass an extra "continuation" function that represents the next step in the computation.

- **Tail-recursive** – A function is tail-recursive if it directly returns the result of the recursive call without doing additional computation.

- **Continuation** – An additional argument to a function that describes “what’s left to do” in order to complete the computation.

### Example:
```sml
fun add x y k = k(x + y)

fun mult x y k = k(x * y)
```


Example call:
```sml 
add 3 4 (fn r => mult r 5 Int.toString)

Step-by-step:

=> [3/x, 4/y, (fn r => mult r 5 Int.toString)/k] k (x+y)

=> (fn r => mult r 5 Int.toString) (3+4)

=> [7/r] mult r 5 Int.toString

=> [r/x, 5/y, Int.toString/k] k(x * y)

=> Int.toString(7 * 5)

=> "35"
```

To return `35` directly, pass `fn r => r` instead of `Int.toString`.

---
## Looking at Types
```sml
add : int -> int -> (int -> a) -> a

mult : int -> int -> (int -> a) -> a
```

---
## Example 1: Sum of a List
### Recursive Way Without CPS
```sml
fun sum [] = 0
  | sum (x :: xs) = x + sum xs
```

### Higher-Order Function (HOF) Version
```sml
fun sum L = foldr (op +) 0 L

val sum = foldr (op +) 0
```

### Tail Recursive Way
```sml
(* tsum: int list * int -> int *)
fun tsum ([], acc) = acc
  | tsum (x :: xs, acc) = tsum(xs, x + acc)
```

### CPS Version
```sml
fun csum [] k = k 0
  | csum (x :: xs) k = csum xs (fn s => k(x + s))
```

---

## Example 2: Success and Failure Continuations
```sml
fun search p Empty sc fc = fc ()
  | search p (Node (l, x, r)) sc fc =
	if p x then sc x
	else search p l sc (fn () => search p r sc fc)
```

---

## Example 3: Fibonacci CPS
```sml
fun fibCPS 0 k = k(0)
  | fibCPS 1 k = k(1)
  | fibCPS n k = fibCPS (n-1) (fn res1 => fibCPS (n-2) (fn res2 => k(res1 + res2)))
```
