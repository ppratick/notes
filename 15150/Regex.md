## Defining Regular Expressions
Regular expressions can be defined using the `datatype` keyword in SML.
### Example:
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

---

**Matching Semantics**
1. Zero matches nothing.
2. One matches the empty string "".
3. Char c matches exactly one character c.
4. Plus (a, b) matches if either a or b matches.
5. Times (a, b) matches if a followed by b matches.
6. Star a matches zero or more repetitions of a.

---

### Example

A regular expression to match the string `"ab"`:
```sml
(* A regular expression to match the string `"ab"`: *)
Times (Char #"a", Char #"b")

(* A regular expression to match a sequence of "a"s: *)
Star (Char #"a")

(* A regular expression to match "a" or "b": *)
Plus (Char #"a", Char #"b")
```


