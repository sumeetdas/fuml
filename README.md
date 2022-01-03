# FUML
FUML (acronym for **Fu**nctional **M**inimal **L**anguage) is a data serialization language inspired from functional programming languages like F# and OCaml. It also borrows some ideas from TOML and YAML.

# Goals

* FUML should be easily readable by humans
* FUML should strive to use as less number of keystrokes as possible.
* FUML must be backed by a schema
* FUML should use common data types used in functional programming languages
* FUML should be portable between different types of programming languages.

# Specs

* FUML documents can be thought to be an instance of a type.
* Following types are allowed in FUML:
    * Boolean
    * Integer
    * Float
    * String
    * Array
    * Map
    * Tuple
    * Record
    * Algebraic Data Type
    * Option
    * Result

## Types

### Boolean
* Boolean values are either `true` or `false` (all lowercase). Example:
```fuml
true
```

Corresponding schema:
```fuml
<schema>

result: bool
```

### Integer
* Integers are whole numbers. Example:
```fuml
2022
```

Corresponding schema:
```fuml
<schema>

result: integer
```
* Its optional to add `+` sign before positive integers. Thus, following two FUML documents
```fuml
+25
```
and 
```fuml
25
```
are valid.
* Negative integers are prefixed with `-`. Example:
```fuml
-25
```
* Undescores can be added to enhance readability:
```fuml
25_000
```

### Float
* Floats should be implemented as IEEE 754 binary64 values. Floats can consist of integer value followed by a fractional part. Example:
```fuml
3.14
```
Here, integer value is `3` and fractional part is `.14`

Corresponding schema
```fuml
<schema>

result: float
```
* Float can also be represented by integer value followed by exponent:
```fuml
4e12
```
Here, integer value is `4` and exponent is `e12`.
    * Integer + fractional part can also precede exponent:
    ```fuml
    4.78e12
    ```
* Float must not be just an integer, or if a decimal point is present, must include both integer and fractional part. Examples of invalid float values:
    * `50`
    * `50.`
    * `.5`

### Strings
* Strings comprises of unicode characters, and are enclosed in single quotes. Example:
```fuml
'Hello world!'
```

Corresponding schema:
```fuml
<schema>

result: string
```

* Escape single quotes and other special characters using `\`:
```fuml
'Hello, \'Peter\''
```

* Multi-line strings must be enclosed within `'''` (triple single quotes):
```fuml
'''
This is first sentence.
This is second sentence.
'''
```
    * Triple quotes `'''` must be in seperate lines. So, following are invalid:
    ```fuml
    ''' This is first sentence.
    This is second sentence. '''
    ```
    * Triple single quotes are not allowed inside multi-line strings.

* If you have a long sentence that you want to split, you can use `'''` followed by `&'<join character>'`. Example:
```fuml
'''&' '
For the binary formats, the representation is made
unique by choosing the smallest representable exponent
allowing the value to be represented exactly.
'''
```
This should be equivalent to the following single-line string:
```fuml
"For the binary formats, the representation is made unique by choosing the smallest representable exponent allowing the value to be represented exactly."
```
    * If `join character` is a single quote `'`, escape it via `\`.

### Array

* Arrays are a list of values having the same data type. Example:
```fuml
[1, 2, 3]
```

Corresponding schema:
```fuml
<schema>

result: int array
```
    * Values in array, if on the same line, are separated by comma.
    * Space between comma is not required, but recommended.
    * Array types are written in postfix notation. `int array` is equivalent to `List<Integer>` in Java.

* Array elements can also be written in separate lines:
```fuml
[
    1
    2
    3
]
```
* You can mix-and-match separating values via comma and writing them in new lines:
```fuml
[
    1, 2
    3, 4
    5,6
]
```

### Tuple

### Map

* Maps are a list of key-value pair. Key and value may have different datatypes, but must be the same across all pairs in the map. Example:
```fuml
'Thousand' => 1_000
'Million' => 1_000_000
```

Corresponding schema:
```fuml
<schema>

result: (string * int) map
```
    * Map pairs are represented as `<key> => <value>`
    * Pairs are modeled as tuples, hence the data type above for each pair is (string * int)

* Difference between a map and an array of pairs as above is that in a map, duplicate keys are not allowed. Thus, the following should throw an error:
```fuml
'Thousand' => 1_000
'Thousand' => 1_000_000
```

### Record

* Records. Example:
```fuml
name = 'Sumeet Das'
username = 'sumeetdas'
```

Corresponding schema:
```fuml
<schema>

type GithubUser =
    name: string
    username: string

result: GithubUser
```

* Some property names aren't just names; they are sentences. You can use round brackets `(` and `)` to use sentences as field names:
```fuml
username: 'sumeetdas'
(has the username completed the course?): true
```
    * Such field names can also include special characters like `?`
    * You can use round brackets `(` and `)` here via escaping them using `\`

* You can directly assign nested property values:
```fuml
fruits.apple.(weight in grams) = 85
```
    * If the nested property does not exist, it should result in an error.

* Nested records. Example:
```fuml
name = 'Sumeet Das'
username = 'sumeetdas'
stats = 
    (number of projects) = 10
    (number of followers) = 20
    stars = 30
```

Corresponding schema:
```fuml
<schema>

type GithubStats = 
    (number of projects): int
    (number of followers): int
    stars: int

type GithubUser = 
    name: string
    username: string
    stats: GithubStats

result: GithubUser
```


### Algebraic Data Type

* 

### Option

* 

### Result

* 
