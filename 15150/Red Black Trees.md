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
