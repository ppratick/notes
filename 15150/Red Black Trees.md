### Binary Search Tree with Red and Black Nodes:
```sml
datatype 'a dict =
    Empty
  | Red of 'a dict * 'a entry * 'a dict
  | Black of 'a dict * 'a entry * 'a dict
```
(Empty considered black)

---
**Red Black Tree (RBT) Invariants:**

1. The tree is sorted on the key part of the entries.

2. The children of a Red node are Black.

3. Each node has a well-defined black height:
	The number of Black nodes on any path from the node down to an Empty is the same.

**Almost RBT (ARBT) Invariants:**
1. (1) and (3) as above
2. LIke (2) from above, but: Red root may have one Red child. 

Invariants imply the tree is roughly balanced:
depth ≤ 2 log₂(|nodes| + 1)

---
## Violations 
Fixed with rotations and recoloring 

![[Screenshot 2025-03-25 at 12.50.22 PM.png | 200]] ![[Screenshot 2025-03-25 at 12.51.02 PM.png | 200]]
### Fixing Violations 
####  `restoreLeft` Function
```sml
(* 
  restoreLeft : 'a dict -> 'a dict

  REQUIRES: Either d is a RBT  
            or d's root is Black,  
            its left child is an ARBT,  
            and its right child a RBT.

  ENSURES: restoreLeft(d) is a RBT,  
           containing exactly the same entries as d,  
           and with the same black height as d.
*)

fun restoreLeft(Black(Red(Red(d1,x,d2),y,d3),z,d4)) = Red(Black(d1,x,d2), y, Black(d3,z,d4))
  | restoreLeft(Black(Red(d1,x,Red(d2,y,d3)),z,d4)) = Red(Black(d1,x,d2), y, Black(d3,z,d4))
  | restoreLeft d = d
```
#### `restoreRight` Function
```sml
(* 
  restoreRight : 'a dict -> 'a dict

  REQUIRES: Either d is a RBT  
            or d's root is Black,  
            **its right child is an ARBT**,  
            and **its left child a RBT**.

  ENSURES: restoreRight(d) is a RBT,  
           containing exactly the same entries as d,  
           and with the same black height as d.
*)

fun
restoreRight(Black(d1,x,Red(d2,y,Red(d3,z,d4)))) =
    Red(Black(d1,x,d2), y, Black(d3,z,d4))

| restoreRight(Black(d1,x,Red(Red(d2,y,d3),z,d4))) =
    Red(Black(d1,x,d2), y, Black(d3,z,d4))

| restoreRight d = d
```
## Inserting 
#### `ins` and `insert` Functions

```sml
(* 
  ins : 'a dict -> 'a dict

  REQUIRES: d is RBT.

  ENSURES: ins(Black(t)) is RBT,  
           ins(Red(t)) is ARBT.  

  Recall: e as (k, v) is in scope.
*)

fun ins (Empty) = Red(Empty, e, Empty)

| ins (Black(l, e' as (k', _), r)) =
    (case String.compare(k, k') of
        EQUAL => Black(l, e, r) (* replace *)
      | LESS => restoreLeft(Black(ins(l), e', r))
      | _ => restoreRight(Black(l, e', ins(r))))

| ins (Red(l, e' as (k', _), r)) =
    (case String.compare(k, k') of
        EQUAL => Red(l, e, r) (* replace *)
      | LESS => Red(ins(l), e', r)
      | GREATER => Red(l, e', ins(r)))

(* 
  insert : 'a dict * 'a entry -> 'a dict

  REQUIRES and ENSURES RBT.
*)

fun insert (d, e as (k, v)) =
    let
    (* ins : 'a dict -> 'a dict
		REQUIRES: d is RBT.
		ENSURES: ins(Black(t)) is RBT,  
		         ins(Red(t)) is ARBT.  
		Recall: e as (k, v) is in scope.*)
        fun ins (Empty) = Red(Empty, e, Empty)
		  | ins (Black(l, e' as (k', _), r)) =
            (case String.compare(k, k') of
                EQUAL => Black(l, e, r) (* replace *)
              | LESS => restoreLeft(Black(ins(l), e', r))
              | _ => restoreRight(Black(l, e', ins(r))))

          | ins (Red(l, e' as (k', _), r)) =
            (case String.compare(k, k') of
                EQUAL => Red(l, e, r) (* replace *)
              | LESS => Red(ins(l), e', r)
              | GREATER => Red(l, e', ins(r)))
    in
        (case ins(d) of
            Red(t as (Red(_, _, _), _, _)) => Black(t)
          | Red(t as (_, _, Red(_, _, _))) => Black(t)
          | d' => d')
    end

(* 
  Here is an acceptable alternate for the case:
*)

(case ins(d) of
    Red(t) => Black(t)
  | d' => d')
  ```
## Lookup
#### `lookup` Function

```sml
(* lookup : 'a dict -> key -> 'a option *)

fun lookup d k =
    let
        fun lk (Empty) = NONE
          | lk (Red t) = lk' t
          | lk (Black t) = lk' t

        and lk' (l, (k', v), r) =
            (case String.compare(k, k') of
                EQUAL => SOME(v)
              | LESS => lk(l)
              | GREATER => lk(r))
    in
        lk d
    end
    ```