# MSON Specification
Markdown Syntax for Object Notation (MSON) is a plain-text syntax for the description and validation of data structures.

In MSON, data structures are described by header-defined _[Named Types][]_ and/or list-defined _[Member Types][]_ and/or
combinations thereof built from a limited set of _[Base Types][]_:

- Named Types

    ```
    # Person (object)
    An individual.

    ## Properties
    - first_name
    - last_name
    - address
        - city
        - street
    ```

- Member Types

    ```
    - person (object) - An individual
        - first_name
        - last_name
        - address
            - city
            - street
    ```

    or, equivalently:

    ```
    - person (Person) - A person
    ```

## 1 How to Read the Grammar
- An arrow (→) mark grammars productions that can be read as "is defined by|is defined by a(n)"
- A double arrow (⇒) marks grammar productions that can be read as "contains|contains a(n)"
- _Italic_ text indicates syntactic categories
- A `code span` indicates literal words and punctuations
- A pipe character (|) separates alternative grammar productions
- A following _[opt]_ indicates optional syntactic categories and literals

### 1.1 Markdown Syntax
Note this reference is using ATX-style headers (#) and hyphens-style lists (-) exclusively. However you MAY use
Setext-style headers and/or asterisk (*) or pluses (+) style lists if you prefer.

### 1.2 Notational Conventions
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in [RFC2119][].

## 2 Base Types
MSON defines a number of distinct base _[Primitive][]_ and _[Structure Types][]_ from which all MSON data structures are
built. Users may extend these [Base Types][] to define new [Named Types][] that MAY be used to build other
[Named Types][] and/or [Member Types][].

#### 2.1 Primitive Types
Applies to basic data structure and type definitions. _[Named Types][]_ and _[Member Types][]_ built directly, or
indirectly, from _Primitive Types_ MUST NOT contain _[Nested Member Types][]_.

- `boolean` or `Boolean`

    Specifies a type with allowed values of `true` or `false`.

- `string`  or `String`

    Specifies any string.

- `number` or `Number`

    Specifies any number.

#### 2.2 Structure Types
Applies to recursive, composite data structure and type definitions. _[Named Types][]_ and _[Member Types][]_ built
directly, or indirectly, from _Structure Types_ MAY contain _[Nested Member Types][]_.

- `array` or `Array`

    Specifies a list of items for values.

- `enum` or `Enum`

    Specifies an exclusive list of possible _[Values][]_ or types for values.

- `object` or `Object`

    Specifies a structure that contains properties as members.

## 3 Member Types
Markdown lists specify MSON _Member Types_, which define the structure and types of individual members and/or
the types of their possible values.

_Member Type_ → _[Property Member Type][]_ | _[Value Member Type][]_

_Member Type_ ⇒ _[Nested Member Types][]_ _[opt]_

```
- person (object) - An individual
    - name (string)
- city (enum) - A particular city
    - San Francisco
    - New York
```

### 3.1 Property Member Type
Defines individual members of `object` type structures.

_Property Member Type_ → `-` _[Property Name][]_ `:` _[opt]_ _[Value Definition][]_ _[opt]_ `-` _[opt]_ _[Description][]_ _[opt]_

_Property Member Type_ ⇒ _[Block Description][]_ _[opt]_

_Property Member Type_ ⇒ _[Nested Member Types][]_ _[opt]_

_Property Member Type_ ⇒ _[Samples][]_ _[opt]_

_Property Member Type_ ⇒ _[Validations][]_ _[opt]_

By default:
- The optional `:` is only applicable in the case where a _[Value Definition][]_ is present and includes a _[Value][]_.
- Any list of _Property Member Types_ collectively MAY define an implied parent `object` type structure.

```
- person (object) - A person
    - first_name
    - last_name
    - address
- company (string)
```

Defines a `person` _Property Member Type_ with a value that is an explicit `object` type structure with members
`first_name`, `last_name`, and `address` and, at the same time, is a member together with `company` of another
implied `object` structure.

#### 3.1.1 Property Name
Defines the name of a property in an `object` type structure.

_Property Name_ → _[Literal Value][]_ | _[Variable Property Name][]_

```
- customer (object)
```

Defines a _[Property Member Type][]_ with a _Property Name_ "customer".

```
- *rel* (string)
```
When a _Property Name_ is a _[Variable Property Name][]_, it indicates a property MAY have an arbitrary name and the
specified _Property Name_ is then a sample. In the prior example, a _Property Member Type_ for a `string` type is defined
with an arbitrary name and a sample value of "rel".

#### 3.1.1.1 Variable Property Name

_Variable Property Name_ → `*`_[Value Definition][]_`*`

In the case of specifying a _[Variable Property Name][]_, the _Value Definition][]_ MAY reference a _[Named Type][]_
that MUST inherit from a `string` type, e.g. to specify a pattern for the variable value.

```
*rel (Custom String)* (object)
```

Where `rel` is a sample value for the _[Property Name][]_ of a _[Property Member Type][]_.

### 3.2 Value Member Type
Defines value type structures. A _Value Type Member_ MUST only be used to define structures of `array` or `enum`
_[Member Types][]_.

_Value Member Type_ → `-` _[Value Definition][]_ _[opt]_ `-` _[opt]_ _[Description][]_ _[opt]_

_Value Member Type_ ⇒ _[Block Description][]_ _[opt]_

_Value Member Type_ ⇒ _[Nested Member Types][]_ _[opt]_

_Value Member Type_ ⇒ _[Samples][]_ _[opt]_

```
- colors (array)
    - red (string) - A sample value
    - green (string)
```

The optional `-` is only applicable in the case where a _[Description][]_ is provided.

#### 3.2.1 Value Definition
Every _[Member Type][]_ specifies type information associated with values in a structure or member in a structure.
This _Value Definition_ information may include literal or sample _[Values][]_ and a _[Type Definition]_, including a
_[Type Specification][]_ and/or _[Type Attributes][]_, of associated types.

_Value Definition_ → _[Value][]_ _[opt]_ _[Type Definition][]_ _[opt]_

A _Value Definition_ of an `object` _[Member Type][]_ MUST NOT specify a _[Value][]_.

```
5, 6 (array)
```

Defines a _Value Definition_ for an `array` type structure with sample values "5" and "6".

#### 3.2.2 Value
Defines either a sample or actual value(s) in a _[Member Type][]_ based on the associated _[Type Definition][]_ in a
_[Value Definition][]_.

_Value_ → _[Literal Value][]_ | _[Variable Value][]_ | _Values List_

_Values List_ → _Value_ | _Value_`,` _Values List_

A _Value_ MAY be a _Values List_ if the associated type structure is an `array` or `enum` type structure.

```
green, red
```

Defines sample values for an `array` type structures or fully-qualified values for an `enum` type
structure. A _Values List_ MUST only be used with an `array` or `enum` _Structure Type_ or a _[Named Type][]_ derived
from a _Structure Type_.

##### 3.2.2.1 Literal Value
Literal value of a type instance. Some limitations apply (see [Reserved Characters & Keywords][]).

```
5
```

##### 3.2.2.2 Variable Value
Defines a _[Value][]_ that is not concrete and specifies a variable _[Property Name][]_ or sample value
indicated using Markdown *italics*.

_Variable Value_ → `*`_[Literal Value][]_`*`

```
*rel*
```

#### 3.2.3 Type Definition
Explicitly specifies the type of an value in an MSON instance.

_Type Definition_ → `(`_[Type Specification][]_ _[opt]_ `,` _[opt]_ _Type Attributes List_ _[opt]_`)`

_Type Attributes List_ → _[Type Attribute][]_ | _[Type Attribute][]_`,` _Type Attributes List_

A _Type Definition_ MUST separate multiple items with a `,`.

```
(enum, optional)
```

##### 3.2.3.1 Type Specification

_Type Specification_ → _[Type Name][]_ | _[Type Name][]_`[`_Nested Type Name List_`]`

_Nested Type Name List_ →  _[Type Name][]_ | _[Type Name][]_`,` _Nested Type Name List_

An `array` or `enum` _[Value Definition][]_ MAY specify the _Type Specifications_ of implied
_[Nested Member Types][]_ members in-line using `[]` as a _Nested Type Name List_.

```
array[number, string]
```

Indicates a `array` type structure MAY include distinct numbers or strings as values.

##### 3.2.3.2 Type Name
References the name of a type in _[Base Types][]_ or _[Named Types][]_. Some limitations apply (see
[Reserved Characters & Keywords][]).

##### 3.2.3.3 Type Attribute
Defines extra attributes associated with the implementation of a type.

- `required` - instance of this type is required
- `optional` - syntactic sugar for optional instance of this type
- `fixed` - indicates the structure and values are fixed for a data structure.

By default:

- If a _[Named Type][]_ or _[Member Type][]_ annotates its type as `fixed`, all _[Nested Member Types][]_ inherit
the `fixed` attribute as well.

    ```
    - person (object, fixed)
        - name
    ```

    Implies:

    ```
    - person (object, fixed)
        - name (fixed)
    ```

- An `array` based _[Named Type][]_ or _[Member Type][]_ MAY specify `fixed` to indicate the structure is a
"fixed ordererd list" of only the specified values and/or types, if any, of its _[Nested Member Types][]_.

    ```
    - colors (array, fixed)
        - red
        - green
    ```

    Implies a fixed-list `array` structure that MUST only contain the two items "red" and "green" in that order.

    ```
    - components (array, fixed)
        - (object)
        - (string)
    ```

    Implies a fixed-list `array` structure that MUST only contain the two items of an arbitrary `object` and `string`
    in that order.

- An `object` based _[Named Type][]_ or _[Member Type][]_ MAY specify `fixed` to indicate a "value object" where all
the properties MUST be present and the values of the properties MUST be the values specified, if any, in its
_[Nested Member Types][]_. Further, such an `object` type structure MUST NOT contain any other properties.

    ```
    - person (object, fixed)
        - first_name: Andrew
        - last_name: Smith
    ```

    Implies a "value object" that MUST contain only the properties "first_name" and "last_name" with the values
    "Andrew" and "Smith", respectively.

    ```
    - person (object, fixed)
        - first_name
        - last_name
    ```

    Implies an `object` that MUST contain only the properties "first_name" and "last_name", respectively.

- Individual _[Nested Member Types][]_ MAY override inherited behavior from a `fixed` inherited type
by using an `optional` attribute and/or MAY indicate values are samples using a _[Variable Value][]_.

    ```
    - person (object, fixed)
        - first_name
        - last_name (optional)
    ```

    Implies a "value object" that MUST contain the property "first_name" and MAY contain the property
    "last_name".

    ```
    - colors (array, fixed)
        - red
        - *green*
    ```

    Implies an `array` type structure that MUST contain "red" as an item and MAY contain any other strings, where
    "green" is a sample value.

#### 3.2.4 Description
Describes a _[Member Type][]_ in-line.

_Description_ → `-` _Markdown-formatted text_

```
- name: Andrew (string) - A Description
```

##### 3.2.4.1 Block Description
Describes an _[Named Type][]_ or a _[Member Type][]_ with a nested (multi-line) text block.

_Block Description_ → _Markdown-formatted text_

Markdown lists that are part of a _[Block Description][]_ are considered part of the block text.

```
- name: Andrew (string) - A Description

    An additional
    multi-line description

    - here
    - there
```

Note that `here` and `there` are NOT _[Member Types][]_ but rather are part of a Markdown list in the
_Block Description_.

#### 3.2.5 Sample
Defines alternate sample _[Values][]_ for _[Member Types][]_ as a nested Markdown list with (multi-line) text.

_Sample_ → `- Sample`

_Sample_ ⇒ _Markdown-formatted text_ | _[Value Member Types][]_

A _[Member Type][]_ MAY have multiple _Sample_ lists.

```
- colors (array)
  - Sample
      - red
  - Sample
      - blue
      - green
```

#### 3.2.6 Validations
Reserved for future use.

### 3.3 Nested Member Types
_[Named Types][]_ and _[Member Types][]_ directly, or indirectly, built from _[Structure Types][]_ MAY contain
_Nested Member Types_, which are defined using nested Markdown lists of allowed _[Member Types][]_.

A _[Member Type][]_ that contains _Nested Member Types_ defines an inner, anonymous type that specifies the structure
of values of that particular member.

_Nested Member Types_ → _nested Markdown-formatted lists of_ _[Member Types][]_ | _Member Type Group_

_Member Type Group_ → `-` _[Member Type Separator][]_

_Member Type Group_ ⇒ _Nested Member Types_

In order to specify _[Nested Member Types][]_ after a _Block Description_, a _[Named Type][]_ or a _[Member Type][]_
MUST use an appropriate _[Member Type Separator][]_ to indicate the end of the _Block Description_ and the beginning
of the _Nested Member Types_ list.

- Named Type

    ```
    ## Person (object)
    An additional
    multi-line description

    - here
    - there

    ## Properties
    - first_name
    - last_name
    ```

- Member Type

    ```
    - person (object)

        An additional
        multi-line description

        - here
        - there

        - Properties
            - first_name
            - last_name
    ```

By Default:

- Un-nested Member Types

    A _[Member Type][]_ that does not contain _Nested Member Types_ and does not contain a _[Type Definition][]_
    implies a `string` _[Type Specification][]_.

    ```
    - count: 1
    ```

    Implies:

    ```
    - count: 1 (string)
    ```

- Object Structures

    A _[Property Member Type][]_ without a _[Type Definition][]_ that contains _[Nested Member Types][]_ implies
    an `object` type structure.

    ```
    - address
        - city
        - state
    ```

    Implies:

    ```
    - address (object)
        - city
        - state
    ```

- Array Structures

    A _[Member Type][]_ with an `array` _[Type Definition][]_ that contains _[Nested Member Types][]_
    specifies an `array` type structure that MAY contain items of the specified type and
    sample values per the particular _[Value Definition][]_.

    ```
    - colors (array)
        - red (string)
        - 5 (number)
    ```

    Implies an `array` structure whose individual items MAY be strings or numbers with sample values "red" and "5",
    respectively.

- Enum Structures

    A _[Member Type][]_ with an `enum` _[Type Definition][]_ that contains _[Nested Member Types][]_
    specifies an `enum` type structure that MUST only contain items of the specified type and
    sample values per the particular _[Value Definition][]_.

    ```
    - colors (enum)
        - red (string)
        - 5 (number)
    ```

    Implies a `colors` _[Property Member Type][]_ that MUST only have a value of the string "red" or the number 5.

    A _[Variable Value][]_ in a _[Nested Member Type][]_ under a `enum` type structure indicates an allowed type
    with an associated sample value.

    ```
    - colors (enum)
        - red (string)
        - *5* (number)
    ```

    Implies Implies a `colors` _[Property Member Type][]_ that MUST have either the string "red" or any number as
    a value, where "5" is a sample of a `number` value.

#### 3.3.1 Member Type Separator
Defines the names of separators to indicate the beginning of a section of _[Nested Member Types][]_.

_Member Type Separator_ →  `Items` | `Members` | `Properties`

- Array Structures - MUST use `Items` for a _Member Type Separator_

- Enum Structures - MUST use `Members` for a _Member Type Separator_

- Object Structures - MUST use `Properties` for a _Member Type Separator_

## 4 Named Types
Defines a new type based on an existing type using Markdown header tags.

_Named Type_ → `#` _[Type Name][]_ _[Type Definition][]_ _[opt]_

_Named Type_ ⇒ _[Block Descriptions][]_ _[opt]_

_Named Type_ ⇒ _[Nested Member Types][]_ _[opt]_

_Named Type_ ⇒ _[Named Member Types Group][]_ _[opt]_

_Named Type_  ⇒ _[Sample][]_

_Named Type_  ⇒ _[Validations][]_

The above order of optional sections MUST be followed. _[Nested Member Types][]_ SHOULD be justified with the
related header.

```
# Person (object)
- first_name
- last_name
```

### 4.1 Named Member Types Group
Defines _[Nested Member Types][]_ in a _[Named Type][]_.

_Named Member Types Group_ → `##` _[Member Type Separator][]_

_Named Member Types Group_ ⇒ _[Nested Member Types][]_

A _Named Member Types Group_ SHOULD be nested one additional header level under
the associated _[Named Type][]_.

- Without a _[Block Description][]_

    _[Nested Member Types][]_ MAY be used without a _Named Member Types Group_ in a _[Named Type][]_.

    ```
    # Person (object)
    - first_name
    - last_name
    ```
- With a _[Block Description][]_

    A _Named Member Types Group_ MUST be used to define _[Nested Member Types][]_.

    ```
    # Person (object)
    Just an ordinary person

    ## Properties
    - first_name
    - last_name
    ```

### 4.3 Generic Named Type
Defines a type that allows an italicized _Type Variable_ to represent a _[Type Name][]_ in a _[Type Specification][]_
for the _Generic Named Type_. A _Type Variable_ MAY be used to indicate any _[Type Name][]_ in a
_[Type Specification][].

A _Type Variable_ MAY only pass to explicitly defined _[Nested Member Types][]_ in the _Generic Named Type_ and MUST
not define any implied _[Nested Member Types][]_.

_Generic Named Type_ → `#` _[Type Name][]_ (

_Variable Type Specification_ → *_Type Variable_*

_Type Variable_ → _[Literal Value][]_

- Inherited type as a variable

    ```
     # Address Decorator (*T*)
        - address

    # Person (object)
        - first_name
        - last_name
    ```

    And:

    ```
    - decorated_person (Address Decorator(Person))
    ```

    Implies the same structure as:

    ```
    - decorated_person (object)
        - first_name
        - last_name
        - address
    ```

- Type passed into explicitly defined _[Member Types][]_

    ```
    # One or Many (*S*[*T*, string])
    - (T)
    - (array[T])
    ```

    And:

    ```
    - rel (One or Many(enum, object))
    ```

    Implies the same structure as:

    ```
    - rel (enum)
        - (object)
        - array(object)
        - (string)
    ```

## 5 Type Inheritance
A _[Member Type][]_ or _[Named Type][]_ that inherits from another _[Named Type][]_ also inherits any
_[Nested Member Types][]_ in the same order they are defined in the inherited _[Named Type][]_ and in order based
on the placement of the _Mixin Type_.

```
# Person (object)
- first_name
- last_name
```

And:

```
- person (Person)
    - address
```

Implies the same structure as:

```
- person (object)
    - first_name
    - last_name
    - address
```

Where the inherited _[Member Types][]_ from `Person` _[Named Type][]_ are listed first.

## 5.1 Mixin Type
MSON defines a _Mixin Type_ that supports multiple inheritance from another _[Named Type][]_. _[Nested Member Types][]_
defined in the inherited _[Named Type][]_ are added at the same indentation level of the _Mixin Type_.

_Mixin Type_ → `- Include` _[Type Name][]_ | `- Include` _[Type Definition][]_

_Example 1_

```
# Person (object)
- first_name
- last_name
```

And:

```
- formal_person (object)
    - prefix: Mr
    - Include Person
```

Implies the same structure as:

```
- formal_person (object)
    - prefix: Mr
    - first_name
    - last_name
```

_Example 2_

Alternately:

```
- formal_person (object)
    - Include Person
    - prefix: Mr.
```

Implies the same structure as:

```
- formal_person (object)
    - first_name
    - last_name
    - prefix: Mr.
```

## 5.2 One Of Type
MSON defines a _One Of Type_ that can be used to describe mutually exclusive sets of _[Nested Member Types][]_. A
_One of Type_ MUST only be used to define _[Property Member Types][]_ for a `object` type structure.

_One of Type_ → `- One Of`

_One of Type_ ⇒ _[Nested Member Types][]_

_One of Type_ ⇒ _Mixin Type_

In order to specify _[Nested Member Types][]_ after a _Block Description_, a _One Of Type_  MUST use an appropriate
_[Member Type Separator][]_ to indicate the end of the _Block Description_ and the beginning
of the _[Nested Member Types][]_ list.

```
- first_name
- One Of
    - last_name
    - One of
        - given_name: Smith
        - suffixed_name: Smith, Sr.
```

Implies values with a structure of:

```
- first_name
- last_name
```

Or:

```
- first_name
- given_name: Smith
```

Or:

```
- first_name
- suffixed_name: Smith, Sr.
```

## 5.3 Member Type Precedence
Implementers of tooling for MSON structures SHOULD use an inheritance precedence such that the last redundant
_[Member Type][]_ specified in a list at the same indentation level appends or overrides a previously defined
_[Member Type][]_.

```
# Person (object, fixed)
- first_name
- last_name
- address (object)
```

- Add/Override Attributes

    _Example 1_

    ```
    - person (Person)
        - last_name (optional)
    ```

    Is literally the same as:

    ```
    - person (object, fixed)
        - first_name (fixed)
        - last_name (fixed)
        - address (object, fixed)
        - last_name (optional)
    ```

    Which implies a structure the same as:

    ```
    - person (object, fixed)
        - first_name (fixed)
        - last_name (optional)
        - address (object, fixed)
    ```

    _Example 2_

    ```
    - person (object)
        - first_name (optional)
        - Include Person
    ```

    Is literally the same as:

    ```
    - person (object, fixed)
        - first_name (optional)
        - first_name (fixed)
        - last_name (fixed)
        - address (object, fixed)
    ```

    Which implies a structure the same as:

    ```
    - person (object, fixed)
        - first_name (fixed)
        - last_name (fixed)
        - address (object, fixed)
    ```

    _Example 3_

    ```
    - person (object)
        - Include Person
        - first_name (optional)

    ```

    Is literally the same as:

    ```
    - person (object, fixed)
        - first_name (fixed)
        - last_name (fixed)
        - address (object, fixed)
        - first_name (optional)
    ```

    Implies a structure the same as:

    ```
    - person (object)
        - first_name (optional)
        - last_name (fixed)
        - address (object, fixed)
    ```

- Add New Member Types

    ```
    - person (Person)
        - citizenship
    ```

    Implies a structure the same as:

    ```
    - person (object, fixed)
        - first_name
        - last_name
        - address (object)
        - citizenship
    ```

- Override Member Types

    ```
    - person (object)
        - Include Person
        - address (string)
    ```

    Is literally the same as:

    ```
    - person (object, fixed)
        - last_name (fixed)
        - address (object, fixed)
        - first_name (optional)
        - address (string)
    ```

    Implies a structure the same as:

    ```
    - person (object)
        - first_name (fixed)
        - last_name (fixed)
        - address (string)
    ```

## 6 Reserved Characters & Keywords
When using following characters or keywords in an _Property Name_, Literal Value or _Type Name_ the name or literal
MUST be enclosed in backticks `` ` ``.

### 6.1 Characters

`:`, `(`,`)`, `<`, `>`, `{`, `}`, `[`, `]`, `_`, `*`, `-`, `+`, `` ` ``

## 6.2 Keywords

`Property`, `Properties`, `Item`, `Items`, `Member`, `Members`, `Include`, `One of`, `Sample`

Note keywords are case-insensitive.

### 6.3 Additional Keywords
Following keywords are reserved for future use:

`Trait`, `Traits`, `Parameter`, `Parameters`, `Attribute`, `Attributes`, `Filter`, `Validation`, `Choice`, `Choices`,
`Enumeration`, `Enum`, `Object`, `Array`, `Element`, `Elements`, `Description`

[RFC2119]: https://www.ietf.org/rfc/rfc2119
[Base Type]: #2-base-types
[Base Types]: #2-base-types
[Primitive]: #21-primitive-types
[Primitive Type]: #21-primitive-types
[Primitive Types]: #21-primitive-types
[Structure Type]: #22-structure-types
[Structure Types]: #22-structure-types

[Member Type]: #3-member-types
[Member Types]: #3-member-types
[Property Member Type]: #31-property-member-type
[Property Member Types]: #31-property-member-type
[Property Name]: #311-property-name
[Variable Property Name]: #3111-variable-property-name
[Value Member Type]: #32-value-member-type
[Value Member Types]: #32-value-member-type
[Value Definition]: #321-value-definition
[Value Definitions]: #321-value-definition
[Value]: #322-value
[Values]: #322-value
[Literal Value]: #3221-literal-value
[Variable Value]: #3222-variable-value
[Type Definition]: #323-type-definition
[Type Definitions]: #323-type-definition
[Type Specification]: #3231-type-specification
[Type Specifications]: #3231-type-specification
[Type Name]: #3232-type-name
[Type Names]: #3232-type-name
[Type Attribute]: #3233-type-attribute
[Type Attributes]: #3233-type-attribute
[Description]: #324-description
[Descriptions]: #324-description
[Block Description]: #3241-block-description
[Block Descriptions]: #3241-block-description
[Sample]: #325-sample
[Samples]: #325-sample
[Validation]: #326-validations
[Validations]: #326-validations
[Nested Member Type]: #33-nested-member-types
[Nested Member Types]: #33-nested-member-types
[Member Type Separator]: #331-member-type-separator

[Named Type]: #4-named-types
[Named Types]: #4-named-types
[Named Member Types Group]: #41-named-member-types-group
[Type Inheritance]: #5-type-inheritance
[Mixin Type]: #51-mixin-inheritance
[One Of Type]: #52-one-of-type
[Reserved Characters & Keywords]: #6-reserved-characters--keywords
