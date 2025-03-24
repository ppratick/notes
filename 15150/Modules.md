# Syntax
### Structure
```sml
signature SIG_NAME =
sig
	...
end
```
### Structure Syntax
```sml
structure Struct_Name : SIGNAME = 
struct
	...
end
```

# Signature vs. Structure Components

| In a **signature**...        | In both                    | In a **structure**...      |
| ---------------------------- | -------------------------- | -------------------------- |
| **type** specifications      | **type** declarations      | **val** declarations       |
| **val** specifications       | **datatype** declarations  | **fun** declarations       |
| **structure** specifications | **exception** declarations | **structure** declarations |
#  Transparent vs. Opaque Ascription

- **Transparent ascription (`:`)** exposes the underlying types  
    - **Motivation**: When the client is meant to know about the types  
    - **Example**: the `List` structure, `PRINTABLE`  

- **Opaque ascription (`:>`)** keeps the types hidden  
    - **Motivation**: When we have invariants we want to maintain  
    - **Example**: stacks, queues, red-black trees, etc.  

- **Neither** can access components that aren’t in the signature  