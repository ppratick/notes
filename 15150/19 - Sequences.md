## Def. 
**Parallelism** exploits multiple processors, evaluating independent code simultaneously.
**Work** estimates the sequential running time on a single processor
**Span** takes account of data dependency, estimates the parallel running time with unlimited processors

---
## Cost Graphs 
A cost graph is a series-parallel graph 
- a directed acylic graph, with source and sink (constant time)
- branching indicates potential parallelism 
![[Pasted image 20250401155025.png | 400]] 
![[Pasted image 20250401155237.png | 400]] 
work = 7, span = 5
### Work and span of a cost graph 
- The **work** is the number of nodes
- The span is the length of the longest path from source to sink 
$span(G) \le work(G)$
--- 
![[Pasted image 20250401155549.png | 400]]
## Brent's Theorem 
An expression with work **w** and span **s** can be evaluated on a **p**-processor machine in time Ω(max(**w**/**p**, **s**)).
![[Pasted image 20250401160655.png | 400]]
work = 10, span = 5

---
## Sequences
```sml
signature SEQ =
sig
  type 'a seq (* abstract *)
  exception Range of string
  val empty : unit -> 'a seq
  val tabulate : (int -> 'a) -> int -> 'a seq
  val length : 'a seq -> int
  val nth : 'a seq -> int -> 'a
  val map : ('a -> 'b) -> 'a seq -> 'b seq
  val reduce : ('a * 'a -> 'a) -> 'a -> 'a seq -> 'a
  val mapreduce : ('a -> 'b) -> 'b -> ('b * 'b -> 'b) -> 'a seq -> 'b
  val filter : ('a -> bool) -> 'a seq -> 'a seq
  val flatten : 'a seq seq -> 'a seq
  val subseq : int * int -> 'a seq -> 'a seq
  val enum : int * int -> int seq
end
```

```sml
val empty : unit -> 'a seq
(* Creates an empty sequence. *)
val tabulate : (int -> 'a) -> int -> 'a seq
(* Builds a sequence by applying a function to each index from 0 to n - 1. *)
(* Work: O(n), Span: O(1) assuming f is constant time *)
val length : 'a seq -> int
(* Returns the number of elements in the sequence. *)
(* Work: O(1), Span: O(1) *)
val nth : 'a seq -> int -> 'a
(* Retrieves the element at a specific index in the sequence. *)
(* Work: O(1), Span: O(1) *)
val map : ('a -> 'b) -> 'a seq -> 'b seq
(* Applies a function to each element in the sequence and returns a new sequence. *)
(* Work: O(n), Span: O(1) assuming f is constant time *)
val reduce : ('a * 'a -> 'a) -> 'a -> 'a seq -> 'a
(* Combines all elements in the sequence using a binary function and a base value. *)
(* Work: O(n), Span: O(log n) *)
val mapreduce : ('a -> 'b) -> 'b -> ('b * 'b -> 'b) -> 'a seq -> 'b
(* Maps a function over the sequence, then reduces the result using a binary function and base value. *)
(* Work: O(n), Span: O(log n) *)
val filter : ('a -> bool) -> 'a seq -> 'a seq
(* Returns a new sequence containing only elements that satisfy a given predicate. *)
(* Work: O(n), Span: O(1) *)
val flatten : 'a seq seq -> 'a seq
(* Flattens a sequence of sequences into a single sequence. *)
(* Work: O(n), Span: O(log n) 
   - where n is the total number of elements across all subsequences *)
val subseq : int * int -> 'a seq -> 'a seq
(* Returns a subsequence of length len starting at index i. Raises Range if out of bounds. *)
(* Work: O(len), Span: O(1) *)
val enum : int * int -> int seq
(* Produces a sequence of integers from i to i + n - 1. *)
(* Work: O(n), Span: O(1) *)
```
### Equivalence
Two sequence values are _extensionally equivalent_ iff they have the same length and have extensionally equivalent items at all positions.
Let: ⟨**v**₀, ..., **v**ₙ₋₁⟩ ≅ ⟨**u**₀, ..., **u**ₘ₋₁⟩ **if and only if** **n** ≅ **m** and for all *i*, **v**ᵢ ≅ **u**ᵢ