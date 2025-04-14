## Functor
A **functor** expects a structure as an argument and produces a structure.

---
## Type Classes
A **type class** is the signature that describes a type and defines some operations that may be performed on that type.  

### Example:
```sml
signature ORD =
sig
    type t
    val compare : t * t -> order
end
```
---
## A Generic Dictionary

A generic dictionary allows mapping from keys of a particular type to values of any type 'a.

### Signature: What the user sees
```sml
signature POLY_DICT =
sig
    structure Key : ORD
    
    (* Mapping keys of type Key.t to values of type ’a *)
    type ’a t
    
    val empty : ’a t
    val insert : Key.t * ’a -> ’a t -> ’a t
    val lookup : Key.t -> ’a t -> ’a option
end
```

### Client-Side Implementation
```sml
functor MkDict (Key : ORD) :> POLY_DICT =
struct
    structure Key = Key
    
    type ’a t = (Key.t * ’a) tree
    
    val empty = Empty
    
    fun insert (k, v) Empty = 
        Node (Empty, (k, v), Empty)
      | insert (k, v) (Node (L, (k', v'), R)) =
          case Key.compare (k, k') of
              EQUAL   => Node (L, (k, v), R)
            | LESS    => Node (insert (k, v) L, (k', v'), R)
            | GREATER => Node (L, (k', v'), insert (k, v) R)

    fun lookup (k, v) Empty = 
        NONE
      | lookup (k, v) (Node (L, (k', v'), R)) =
          case Key.compare (k, k') of
              EQUAL   => SOME v'
            | LESS    => lookup (k, v) L
            | GREATER => lookup (k, v) R
end
```

