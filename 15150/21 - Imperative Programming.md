## Mutation and References

- New type: `t ref` — a reference to a mutable value of type `t`
  - Created with `ref e` (evaluates `e`, returns a new cell)
  - Access with `!e` (dereferences to get the value)
  - Update with `e1 := e2` (updates the referenced cell)

### Typing Rules
```sml
ref e       : t ref     if e : t
!e          : t         if e : t ref
e1 := e2    : unit      if e1 : t ref and e2 : t
```

### Example
```sml
val c = ref 7
val v = !c         (* v = 7 *)
val () = c := 4    (* update to 4 *)
```

## Aliasing

- Multiple variables can refer to the same cell.
```sml
val c = ref 10
val d = c          (* d is an alias for c *)
val () = d := 42
val v = !c         (* v = 42 *)
```

### Caution
Aliased cells mean changes through one alias affect the others.

## Cells in Cells

```sml
val cc = ref (ref 9) : int ref ref
```
- Reference cells can nest; e.g., a cell containing another cell.

## Circular Structures

```sml
datatype 'a chain = Val of 'a | Link of 'a chain ref

val (x as Link c) = Link (ref (Val 7))
val () = c := x     (* creates a cycle *)
```

## Equality

- Comparing values: `!c = !d`
- Comparing references: `c = d`
- Different reference cells with the same contents are **not** equal.

## Sequential Expressions

```sml
(e1; e2; ...; en)
```
- Evaluates expressions left to right.
- Type: type of `en`
- Value: value of `en`

Example:
```sml
let val c = ref 10 in
  (print(Int.toString(!c)); c)
end
```

## Race Conditions

```sml
fun deposit a n = a := !a + n
fun withdraw a n = a := !a - n
val chk = ref 100
val _ = (deposit chk 50, withdraw chk 80)
```

- Parallel mutation can cause nondeterminism.
- Possible results: 70, 20, 150, or even garbage depending on timing.

## Benign Effects

- Localized mutation for efficiency that doesn’t affect program correctness.
- Used in:
  - Graph traversal
  - Random number generators
  - Stream memoization

## Example: Reachability in Graph

```sml
type graph = int -> int list
```

### Naive DFS (can loop):
```sml
fun reach g (x, y) =
  let fun dfs n = (n = y) orelse List.exists dfs (g n)
  in dfs x end
```

### With Visited List:
```sml
fun reachable g (x, y) =
  let
    val visited = ref []
    fun mem n = List.exists (fn x => n = x)
    fun dfs n = (n = y) orelse
                (not (mem n (!visited)) andalso
                 (visited := n :: (!visited);
                  List.exists dfs (g n)))
  in dfs x end
```

## Random Number Generator

```sml
signature RANDOM = sig
  type gen
  val init : int -> gen
  val random : gen -> int -> int
end
```

Uses mutable state to update an internal seed.

## Stream Memoization

```sml
fun delay d =
  let
    val cell = ref d
    fun memoFn () =
      let val r = d() in
        (cell := (fn () => r); r)
      end handle E => (cell := (fn () => raise E); raise E)
    val _ = cell := memoFn
  in
    Stream (fn () => !cell())
  end
```

- Ensures a computation is performed only once.
- Stores the result or raised exception.
