```sml
(* 1. map : ('a -> 'b) -> 'a list -> 'b list *)
fun map f [] = []
  | map f (x::xs) = (f x) :: (map f xs)

  
(* 2. filter : ('a -> bool) -> 'a list -> 'a list *)
fun filter p [] = []
  | filter p (x::xs) = if p x then x :: (filter p xs) else (filter p xs)

  
(* 3. foldr : ('a * 'b -> 'b) -> 'b -> 'a list -> 'b *)
fun foldr f z [] = z
  | foldr f z (x::xs) = f (x, foldr f z xs)

  
(* 4. foldl : ('a * 'b -> 'b) -> 'b -> 'a list -> 'b *)
fun foldl f z [] = z
  | foldl f z (x::xs) = foldl f (f (x, z)) xs

  
(* 5. o : ('a -> 'b) * ('c -> 'a) -> ('c -> 'b) *)
infix o
fun f o g = fn x => f (g x)
```

1. **map** – Applies a function to each element of a list and returns a new list with the results.
2. **filter** – Returns a new list containing only the elements that satisfy a given predicate.
3. **foldr** – Combines elements of a list from **right to left** using a function and a base value.
4. **foldl** – Combines elements of a list from **left to right** using a function and a base value.
5. **o** – Function composition; combines two functions into one.