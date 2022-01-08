# FUML
FUML (acronym for **Fu**nctional **M**inimal **L**anguage) is a data serialization language inspired from functional programming languages like F# and OCaml. It also borrows some ideas from TOML and YAML.

# Goals

* FUML should be easily readable by humans
* FUML should strive to use as less number of keystrokes as possible.
* FUML must be backed by a schema
* FUML should use common data types used in functional programming languages
* FUML should be portable between different types of programming languages.

# Specs

## Comments

* You can add single-line comment like so:

    ```fuml
    // the answer
    42
    ```

* You can also add multi-line comments like so:

    ```fuml
    (*
        42 is the answer to the “ultimate question of life,
        the universe, and everything”
    *)
    42
    ```

* You can also add both types of comments in the FUML schema:

    ```fuml
    <schema>

    // returns 
    (*
        the answer to question of life, the universe
        and everything
    *)
    data: int
    ```

## Types

* FUML documents can be thought to be an instance of a type.
* Following types are allowed in FUML:
    * Boolean
    * Integer
    * Float
    * String
    * List
    * Map
    * Tuple
    * Record
    * Algebraic Data Type
    * Option
    * Result
    * Date
    * Type alias

### Boolean
* Boolean values are either `true` or `false` (all lowercase). Example:
    ```fuml
    true
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: bool
    ```

### Integer
* In FUML, integers are natural numbers. 
    * Natural numbers are any number (positive or negative) which does not have any fraction part. Thus, `-12` is a natural number while as `12.40` is not.
* FUML has following data types:
    * `i8`
        * Range = -128 to 127
    * `i16`
        * Range = -32,768 to 32,767
    * `i32`
        * Range = -2,147,483,648 to 2,147,483,647
    * `i64`
        * Range = -2^63 to (2^63 - 1)
    * `i128`
        * Range = -2^127 to (2^127 - 1)
    * `u8`
        * Range = 0 to 255
    * `u16`
        * Range = 0 to 65,536
    * `u32`
        * Range = 0 to 4,294,967,296
    * `u64`
        * Range = 0 to 2^64
    * `u128`
        * Range = 0 to 2^128

* Example:
    ```fuml
    2022
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: i32
    ```

* There's also an `int` data type, which is a type alias for `i32`:
    ```fuml
    2_147_483_647
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: int
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

    data: float
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

    data: string
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
    '''&'+'
    For the binary formats, the representation is made
    unique by choosing the smallest representable exponent
    allowing the value to be represented exactly.
    '''
    ```
    This will be equivalent to the following single-line string:
    ```fuml
    'For the binary formats, the representation is made+unique by choosing the smallest representable exponent+allowing the value to be represented exactly.'
    ```
    * If `join character` is a single quote `'`, escape it via `\`.

### List

* Lists are a collection of values having the same data type. Example:
    ```fuml
    [
        1
        2
        3
    ]
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: int list
    ```
    * Space between comma is not required, but recommended.
    * List types are written in postfix notation. `int list` is equivalent to `List<Integer>` in Java.

* List elements can also be written in compact form as below:
    ```fuml
    [1, 2, 3]
    ```

    * Values in list, if on the same line, are separated by comma.

* You can mix-and-match separating values via comma and writing them in new lines:
    ```fuml
    [
        1, 2
        3, 4
        5,6
    ]
    ```

### Tuple

* Tuple. Example:

    ```fuml
    (
        2 
        [ 
            'apple'
            'banana' 
        ]
        'fruits'
    )
    ```

    Compact form:
    ```fuml
    (2, ['apple', 'banana'], 'fruits')
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: int * (string list) * string
    ```

### Map

* Maps are a list of key-value pair. Key and value may have different datatypes, but must be the same across all pairs in the map. Example:
    ```fuml
    {
        'Thousand' => 1_000
        'Million' => 1_000_000
    }
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: (string * int) map
    ```
    * Map pairs are represented as `<key> => <value>`
    * Pairs are modeled as tuples, hence the data type above for each pair is (string * int)

* Only integer, float and string data types are allowed for map keys. Using any other data type should throw an error.

* Difference between a map of key-value pairs and a similar list of pairs is that in a map, duplicate keys are not allowed. Thus, the following should throw an error:
    ```fuml
    'Thousand' => 1_000
    'Thousand' => 1_000_000
    ```

    while as this is allowed in a list:
    ```fuml
    [
        ('Thousand', 1_000)
        ('Thousand', 1_000_000)
    ]
    ```

* Maps can also be written in compact form as below:
    ```fuml
    {'Thousand'=>1_000;'Million'=>1_000_000}
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

    data: GithubUser
    ```

* Records can also be written in compact form:
    ```fuml
    {name='Sumeet Das';username='sumeetdas'}
    ```

* Some property names aren't just names; they are sentences. You can use round brackets `(` and `)` to use sentences as field names:
    ```fuml
    username: 'sumeetdas'
    (has the user completed the course?): true
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

    data: GithubUser
    ```

* List of records:
    ```fuml
    [
        name = 'Cat'
        sound = 'meow'
        ;
        name = 'Dog'
        sound = 'woof'
    ]
    ```

    Compact form:
    ```fuml
    [{name='Cat';sound='meow'};{name='Dog';sound='woof'}]
    ```

    Corresponding schema:
    ```fuml
    <schema>

    type Animal = 
        name: string
        sound: string

    data: Animal list
    ```

* Nested list of records:
    ```fuml
    animals = [
        name = 'Cat'
        sound = 'meow'
        ;
        name = 'Dog'
        sound = 'woof'
    ]
    ```

    You can also use compact form of records as below:
    ```fuml
    animals = [
        {name = 'Cat'; sound = 'meow'}
        {name = 'Dog'; sound='woof'}
    ]
    ```
    * If one record is written in compact form, others too must follow the same pattern.
    * Compact form records don't need semicolon `;` in between if they are written in separate lines.

* Map to a record:
    ```fuml
    'Cat' => 
        family = 'Felidae'
        sound = 'meow'
    'Dog' => 
        family = 'Canidae'
        sound = 'woof'
    ```

    Compact form:
    ```
    {'Cat' => {family='Felidae';sound='meow'};'Dog'=>{family='Canidae' ; sound = 'woof'}}
    ```

### Algebraic Data Type

* Algebraic data types:
    ```fuml
    Circle 5
    ```

    ```fuml
    Rectangle (5, 3)
    ```

    ```fuml
    Polygon 
        numberOfSides = 5
        sideLengths = [4, 4, 4, 4, 4]
    ```

    ```fuml
    NoShape
    ```

    Corresponding schema:
    ```fuml
    <schema>

    type Sides = 
        numberOfSides: int
        sideLengths: int list

    type Shape = 
        | Circle of int
        // Length * Breadth
        | Rectangle of int * int
        | Polygon of Sides
        | NoShape

    data: Shape
    ```

* You can also use one of the algebraic data types:

    ```fuml
    5
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: Shape.Circle
    ```
    * For types like these, you only need to provide the parameter values. For example, here you only need to specify `5` as the `int` value an instance of `Shape.Circle` type expects.
    * Sum types in algebraic data types (e.g. `Circle` and `Rectangle` in `Shape` data type), if used as a data type for a property, requires fully qualified name. For example, you cannot use `Circle` type as follows:
        ```fuml
        <schema>

        type Shape = 
            | Circle of int

        data: Circle
        ```
    * Instead, the correct way to use `Circle` type is to use its fully qualified name `Shape.Circle`:
        ```fuml
        <schema>

        //.. 

        data: Shape.Circle
        ```

* A schema must not define any property whose type is an algebraic data type with no parameters. For example, the following is an invalid schema:

    ```fuml
    <schema>

    type Shape =
        | NoShape

    data: Shape.NoShape
    ```

    as `NoShape` type expects no parameters and hence makes no sense to use it as a data type.

### Option

* Option. Example:

    ```fuml
    None
    ```

    ```fuml
    Some 2
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: int option
    ```

* You can also drop `Some` and directly write the value. Thus, the following FUML document:

    ```fuml
    2
    ```

    would be valid for above schema.

### Result

* Result. Example:

    ```fuml
    Ok 5
    ```

    ```fuml
    Error 'error happened'
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: (int * string) result
    ```

### Date

* Date. Example:

    ```fuml
    Timestamp ''
    ```

### Type alias

* You can use type alias to rename a type.
* Example:
    ```fuml
    (50, 'Jay')
    ```

    Corresponding schema:
    ```fuml
    <schema>

    // type alias
    type User = int * string

    data: User
    ```

* FUML recommendations for naming type aliases:
    * It should follow CamelCase convention
    * The first letter should be uppercase
* Type alias definition must be in a single line. The following is invalid:
    ```fuml
    <schema>

    // invalid; should throw an error
    type User =     
        int * string
    ```
* You cannot name a type alias as any one of the lowercase pre-defined types:
    * any of the integer types like `int` and `i64`
    * `float`
    * `string`
    * `map`
    * `list`
    * `option`

    So, the following would result in an error:
    ```fuml
    <schema>

    type list = i32
    ```

