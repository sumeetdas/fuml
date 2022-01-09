# FUML
FUML (acronym for **Fu**nctional **M**inimal **L**anguage) is a data serialization language inspired from functional programming languages like F# and OCaml. It also borrows some ideas from TOML and YAML.

# Goals

* FUML should be easily readable by humans
* FUML should be easy to type.
* FUML must be backed by a schema
* FUML should support common data types used in functional programming languages
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
    * Sum Type
    * Option
    * Result
    * DateTime
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

* Tuples are data types which can store multiple types of data in a specific order. Example:

    ```fuml
    (2, ['apple', 'banana'], 'fruits')
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: int * (string list) * string
    ```

* Tuple values are seperated by a comma `,` and are enclosed within a pair of round brackets `(` and `)`.

* Tuples must be written in a single line. 
    * If the tuple size goes big, then you should consider modeling the data as a record which allows for a more readable format. 
    * Record also allows you to name the values, something which is not possible in a tuple.

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

* FUML recommendations for naming record types:
    * It should follow CamelCase convention
    * The first letter should be uppercase

* Some property names aren't just names; they are sentences. You can use round brackets `(` and `)` to use sentences as property names:
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

### Sum Type

* Sum types are data structures that can take on several different, but fixed, types. 
* To understand it, lets consider the following example - Suppose you want to create a type called `Shape` which can accept instances of different types of shapes like `Circle`, `Rectangle`, `Polygon`. Or if you don't have any shape to store, the type will accept a `NoShape` instance. 
To implement it, you can define `Shape` as a sum type:

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

    This schema can accept the following FUML documents, each of which represents an instance of a shape type:

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


* FUML recommendations for naming sum types:
    * It should follow CamelCase convention
    * The first letter should be uppercase

* You can also use one of the sum types:

    ```fuml
    5
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: Shape.Circle
    ```
    * For types like these, you only need to provide the parameter values. For example, here you only need to specify `5` as the `int` value an instance of `Shape.Circle` type expects.
    * Individual types in sum types (e.g. `Circle` and `Rectangle` in `Shape` data type), if used as a data type for a property, requires fully qualified name. For example, you cannot use `Circle` type as follows:
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

* A schema must not define any property whose type is a sum type with no parameters. For example, the following is an invalid schema:

    ```fuml
    <schema>

    type Shape =
        | NoShape

    data: Shape.NoShape
    ```

    as `NoShape` type expects no parameters and hence makes no sense to use it as a data type.

### Option

* Option is a sum type which is defined as follows:
    ```fuml
    type 't Option = 
        | Some of 't
        | None
    ```
    where `'t` could be any type.

* This data type is useful when you want to model a nullable data. In other words, a property which may or may not have a value.

* Example:

    ```fuml
    None
    ```

    ```fuml
    Some 2
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: int Option
    ```

    * If the value is present, use `Some <value>`
    * If the value is not present, use `None`

* This type has `None` as default. If a property is not included in the FUML document, then its value is assumed to be `None`.

* You can also drop `Some` and directly write the value. Thus, the following FUML document:

    ```fuml
    2
    ```

    would be valid for above schema.

### Result

* Result is a sum type which is defined as follows:
    ```fuml
    type 'x, 'y Result = 
        | Ok of 'x
        | Error of 'y
    ```

* Result type can be used in cases when you want to return result of an operation if its successful, or an error response in case of failure.

* Example:

    ```fuml
    Ok 5
    ```

    ```fuml
    Error 'error happened'
    ```

    Corresponding schema:

    ```fuml
    <schema>

    data: (int * string) Result
    ```

### DateTime

* DateTime is a sum type used to represent multiple formats of date and time. DateTime type is defined as:
    ```fuml
    type DateTime = 
        // example: 1985-04-12T23:20:50.123456Z (T can be omitted)
        | UtcDateTime of string

        // example: 1996-12-19T16:39:57-08:00 (T can be omitted)
        | OffsetDateTime of string

        // example: 1996-12-19T16:39:57.123456-08:00 (T can be omitted)
        | OffsetWithFractionDateTime of string
        
        // example: 1996-12-19
        | YearMonthDate of string
        
        // example: 07:32:00
        | LocalTime of string

        // example: 00:32:00.123456
        | LocalTimeWithFraction of string
    ```

* Using incorrect date or time format with a given Format type must throw an error. For example, the following will result in an error:
    ```fuml
    UtcDateTime '1996-12-19'
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: DateTime
    ```

* Oftentimes, we don't accept multiple date-time formats. To specify which format to accept, you can use fully qualified name of Format type as the data type. 
For example, if you want to use OffsetDateTime as the format, you can do so as follows:
    ```fuml
    '1996-12-19T16:39:57-08:00'
    ```

    Corresponding schema:
    ```fuml
    <schema>

    data: DateTime.OffsetDateTime
    ```

    * Using Format data type would allow you to directly use the string containing date and/or time.

* Date and time formats follow the [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) specs.

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

    So, the following would result in an error:
    ```fuml
    <schema>

    type list = i32
    ```

* If there are two type alias definitions, the latest definition would be considered. Example:
    ```fuml
    <schema>

    type WholeNumber = i8

    type WholeNumber = i32
    ```

    Here, `WholeNumber` would be a type alias for `i32`.

    * One corollary of this rule is that you can define a type alias named `DateTime`. This would effectively replace the existing `DateTime` sum type with the new type alias. For example:
    ```fuml
    <schema>

    type DateTime = string
    ```

    would make `DateTime` an alias of type `string`.

## Files and Namespaces

### FUML Files

* Any type of FUML content, be it FUML document or FUML schema, must be stored in files with extension `.fuml`.
* Schema file names must start with either an underscore (`_`) or an uppercase letter, followed by any number of uppercase letters, lowercase letters, underscores and digits. Examples:
    ```
    _Schema.fuml

    Schema_123.fuml
    ```

* Schema files must have the tag `<schema>` at the beginning of the file. Example:
    ```fuml
    <schema>

    type User = 
        name: string
        username: string

    // ...
    ```

* Every schema file must end with `data: <type-name>`.

### FUML Namespaces

* FUML schema can be split in multiple files. However, all FUML files must be stored under one directory.
* Typical folder tree involving large number of FUML schemas might look the following:
    ```sh
    <base-directory>
    |
    |--- base.fuml (optional)
    |--- SchemaA.fuml
    |--- SchemaB.fuml
    |--- NamespaceA
        |--- SchemaC.fuml
        |--- SchemaD.fuml
    |--- NamespaceB
        |--- SchemaE.fuml
        |--- SchemaF.fuml
    ```

* Directories inside `<base-directory>` are called **Namespaces**. 
    * They are used to group similar schema files together. 

    * They also allow using schemas with same name by storing them under different namespaces.

    For example, consider you want to store Twitter and Github user data, and would want to create schema named `User.fuml` to model it. Since you cannot store two files named `User.fuml` in a single directory, you create two namespaces `Twitter` and `Github` and create `User.fuml` schema in each namespace.

    * `<base-directory>` is called as **root namespace**.

* `base.fuml` is a file which contains information about the order in which FUML files need to be compiled. 
    * If the file is not present, the default order of compilation is recursively compile namespaces in alphabetical order. In each namespace, schema files would be compiled in alphabetical order.

    * To define compile order, use `compile` keyword. 
    
    * For example, if you want to compile `NamespaceA` schemas, then `NamespaceB` schemas, followed by `SchemaB.fuml` and `SchemaA.fuml` in root namespace, the `base.fuml` contents would look like:
        ```fuml
        compile 'NamespaceB'
        compile 'NamespaceA'
        compile 'SchemaB.fuml'
        compile 'SchemaA.fuml'
        ```

    * Schemas in `NamespaceB` and `NamespaceA` in the above example would be compiled in alphabetical order. If you want to change the order of compilation for `NamespaceB`, you need to explicitly specify the order for all files in the namespace:
        ```fuml
        compile 'NamespaceB.SchemaD.fuml'
        compile 'NamespaceB.SchemaC.fuml'
        ```

### Import schemas

* Schemas are imported automatically, provided they have been compiled before. 
For example, if `SchemaB` is a schema stored in `SchemaB.fuml` file, and `SchemaC` is another schema stored in `SchemaC.fuml` file under `NamespaceA` directory, then you can make use of these two schemas directly as follows:
    ```fuml
    <schema>
    type ComplexType = 
        someProperty: SchemeB
        anotherProperty: NamespaceA.SchemaC

    data: ComplexType
    ```

    provided the `base.fuml` file compiles `SchemaB` and `SchemaC` before `ComplexType`:
    ```fuml
    compile 'NamespaceA'
    compile 'SchemaB'
    compile 'ComplexType'
    ```

* If the schemas are not compiled before importing them, then it would result in an error.

* Namespace schemas will still be referenced using their fully qualified names (e.g. `NamespaceA.SchemaC`) as opposed to using just their names (e.g. `SchemaC`) when used as a property's data type.

## Property metadata

* By default, all properties are required (meaning they need to have valid values), except for optional type which has `None` as default.   
But what if you want to use some default value when a property is missing? To allow that, you can make use of property metadata syntax.

* In FUML schemas, you can provide additional metadata about the property via the following syntax:
    ```fuml
    <schema>

    data: i32
        metadata1 = 2
        metadata2 = <some value>
        // ...
    ```

* Metadata is allowed only for properties having integer, float or string types, or having type as a type alias mapping to one of these three types. 

* Using metadata syntax, you can specify the default value as follows:
    ```fuml
    <schema>

    type Fruit = 
        name: string
        producer: string
            default = 'Fruit company'
        (price per kg): float
            default = 4.0

    data: Fruit
    ```

    In this schema, `name` property is required as there's no default value defined for it, while as `producer` and `(price per kg)` properties are optional. If `producer` is missing, its value would be `'Fruit company'`, while as if `(price per kg)` is missing then its value would be `4.0`.

# License

MIT License, Copyright (c) 2022 Sumeet Das