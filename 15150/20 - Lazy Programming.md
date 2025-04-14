## Suspensions 
A **suspension** of type `τ` is a function of type  `unit -> τ`.
We say a suspension is **forced** when it is applied to argument `()`.

If `e : t`, then `(fn () => e)` is a suspension of type `t`.

We view such a suspension as a **lazy** representation of `e`.  
The suspension is a function, so `e` will not be evaluated  
until the suspension is forced.

---
## Streams 
We will build our own streams. 
**Caution:** These are different from SML's build-in I/O streams. 

We will model (potentially infinite) streams  of data much like lists, but lazily:

- **Base case**: *an empty stream*
- **Inductive case (Also called Co-Induction where there is no base cases)**:  
	- *A suspension of the following:*  
	  *A single element*  “*consed*” *onto another stream*.

*(Suspensions will allow us to build streams with no base cases! Never-ending, infinite, yet encapsulated finitely)*
```sml
signature STREAM =
sig
  type 'a stream    (* abstract *)
  datatype 'a front = Empty | Cons of 'a * 'a stream

  val expose : 'a stream -> 'a front
  (* Reveals the front of a stream (either Empty or Cons). *)
  val delay : (unit -> 'a front) -> 'a stream
  (* Creates a lazy stream from a thunk (a delayed computation). *)
  
  val empty : 'a stream
  (* Represents an empty stream. *)
  val cons : 'a * 'a stream -> 'a stream
  (* Prepends an element to a stream. *)
  val null : 'a stream -> bool
  (* Checks if the stream is empty. *)
  val take : ('a stream * int) -> 'a list
  (* Takes the first n elements of a stream and returns them as a list. *)
  
  val map : ('a -> 'b) -> 'a stream -> 'b stream
  (* Applies a function to each element of a stream, returning a new stream. *)
  val filter : ('a -> bool) -> 'a stream -> 'a stream
  (* Returns a stream with only the elements that satisfy the predicate. *)
  
  (* ... more functions: append, tabulate, zip ... *)
end
```

```sml
structure Stream : STREAM =
struct
  datatype 'a stream = Stream of unit -> 'a front
  and 'a front = Empty | Cons of 'a * 'a stream

  fun delay (d) = Stream(d)
  fun expose (Stream(d)) = d ()

  val empty = Stream (fn () => Empty)
  fun cons (x, s) = Stream (fn () => Cons(x, s))
  
  fun filter p s = delay (fn () => filter' p (expose s))
  and filter' p Empty = Empty
    | filter' p (Cons(x, s')) =
        if (p x) then Cons(x, filter p s')
        else filter' p (expose s')

  fun null s = (case (expose s) of Empty => true | _ => false)

  fun map f s = delay (fn () => map' f (expose s))
  and map' f Empty = Empty
    | map' f (Cons(x, s')) = Cons(f x, map f s')

  (* Second version: inline version without helper *)
  fun map f s =
    delay (fn () =>
      case (expose s) of
        Empty => Empty
      | Cons(x, s') => Cons(f x, map f s'))
end
```

![[Screenshot 2025-04-08 at 1.15.37 PM.png | 400]]

---
We say that two streams **X** and **Y** produced by the same structure `Stream : STREAM` are extensionally equivalent (**X ≅ Y**) if and only if `Stream.take(X, n) ≅ Stream.take(Y, n)`  
for all integers `n ≥ 0`.

### Productive Streams 
We say a stream **s** of type **t Stream.stream** is _productive_ if and only if **Stream.expose(s)** returns one of the following:
	a) **Stream.Empty** or
	b) **Stream.Cons(x,s’)**, with **x** a value of type **t** and **s’** itself a productive stream.
	