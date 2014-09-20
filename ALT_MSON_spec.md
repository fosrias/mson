# MSON Specification
Markdown Syntax for Object Notation (MSON) is a plain-text syntax for the description and validation of data structures.

In MSON, data structures are described by header-defined _[Named Types][]_ and/or list-defined _[Member Types][]_ and/or
combinations thereof:

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
- Grammar productions can span several lines and end at another grammar production and/or the end of a paragraph

### 1.1 Markdown Syntax
Note this reference is using ATX-style headers (#) and hyphens-style lists (-) exclusively. However you MAY use
Setext-style headers and/or asterisk (*) or pluses (+) style lists if you prefer.

### 1.2 Notational Conventions
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in [RFC2119][].

## 2 Types
All data structures in MSON are built from distinct data types.

### 2.1 Base Types
MSON defines a number of base primitive and structure types.

#### 2.1.1 Primitive Types
Applies to basic data structure and type definitions. _[Named Types][]_ and _[Member Types][]_ built directly, or
indirectly, from _Primitive Types_ MUST NOT contain _[Nested Member Types][]_.

- `boolean` or `Boolean`

    Specifies a _[Literal Value][]_ of `true` or `false`.

- `string`  or `String`

    Specifies any string.

- `number` or `Number`

    Specifies any number.

#### 2.1.2 Structure Types
Applies to recursive, composite data structure and type definitions. _[Named Types][]_ and _[Member Types][]_ built
directly, or indirectly, from _Structure Types_ MAY contain _[Nested Member Types][]_.

- `array` or `Array`

    Specifies an un-ordered list of items for values.

- `enum` or `Enum`

    Specifies an exclusive list of possible _[Values][]_ or _[Types][]_ for values.

- `object` or `Object`

    Specifies a structure that contains properties as members.

### 2.2 Custom Types
Users may extend these _[Base Types][]_ to define new _[Named Types][]_ that MAY be used to build other
_[Named Types][]_ and/or _[Member Types][]_.

## 3 Member Types
Markdown lists specify MSON _Member Types_, which define the struture and types of individual members and/or
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

### 3.1 Property Member Types
Defines members of `object` type structures.

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

_Property Name_ → _[Literal Value][]_ | _[Variable Value][]_

```
- customer (object)
```

Defines a _[Property Member Type][]_ with a _Property Name_ "customer".

```
- *rel* (string)
```
When a _Property Name_ is a _[Variable Value][]_, it indicates a property MAY have an arbitrary name and the
specified _Property Name_ is then a sample. In the prior example, a _Property Member Type_ for a `string` type is defined
with an arbitrary name and a sample value of "rel".

### 3.2 Value Member Types
Defines list-related type structures. _Value Type Members_ MUST only be used to define structures of `array` or `enum`
_Member Types.

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

#### 3.2.1 Value Definitions
Every _[Member Type][]_ specifies type information associated with values in a structure or member in a structure.
This _Value Definition_ information may include literal or sample _[Values][]_ and a _[Type Definition]_, including a
_[Type Specification][]_ and/or _[Type Attributes][]_, of associated types.

_Value Definition_ → _[Value][]_ _[opt]_ _[Type Definition][]_ _[opt]_

A _Value Definition_ of an `object` _[Member Type][]_ MUST NOT specify a _[Value][]_.

```
5, 6 (array)
```

Defines a _Value Definition_ for an `array` type structure with sample _[Values][]_ "5" and "6".

##### 3.2.1.1 Values
Defines either sample or actual values in a _[Member Type][]_ based on the associated _[Type Definition][]_ in a
_[Value Definition][]_.

_Value_ → _[Literal Value][]_ | _[Variable Value][]_ | _Values List_

_Values List_ → _Value_ | _Value_`,` _Values List_

A _Value_ MAY be a _Values List_ if the associated type structure is a list-related structure.

```
green, red
```

Defines sample values for an `array` type structures or fully-qualified values for an `enum` type
structure. A _Values List_ MUST only be used with a list-related _Structure Type_ or a _[Custom Type][]_ derived from a
_Structure Type_.

###### 3.2.1.1.1 Literal Value
Literal value of a type instance. Some limitations apply (see [Reserved Characters & Keywords][]).

```
5
```

A _[Value][]_ in a _[Value Definition][]_ with an unspecified type MAY imply the type of the related _[Member Type][]_,
for example `number` in this example.

###### 3.2.1.1.2 Variable Value
Defines a _Value_ that is not concrete and is used to indicate variable _[Property Names][]_ or sample values indicated
using Markdown *italics*.

_Variable Value_ → `*`_[Literal Value][]_`*` | `*`_[Value Definition][]_`*`

```
*rel*
```

In the case of specifying a variable _[Property Name][]_, the _Variable Value_ MAY reference a _[Named Type][]_
that MUST inherit from a `string` type, e.g. to specify a pattern for the variable value.

```
_rel (Custom String)_ (object)
```

Where `rel` is a sample value for the _[Property Name][]_ of a _[Property Member Type][]_.

##### 3.2.1.2 Type Definition
Explicitly specifies the type of an value in an MSON instance.

_Type Definition_ → `(`_[Type Specification][]_ _[opt]_ `,` _[opt]_ _Type Attributes List_ _[opt]_`)`

_Type Attributes List_ → _[Type Attribute][]_ | _[Type Attribute][]_`,` _Type Attributes List_

A _Type Definition_ MUST separate multiple items with a `,`.

```
(enum, optional)
```

##### 3.2.1.3 Type Specification

_Type Specification_ → _[Type Name][]_ | _[Type Name][]_`[`_Type Name List_`]`


_Type Name List_ →  _[Type Name][]_ | _[Type Name][]_`,` _Type Name List_

An `array` or `enum` _[Value Definition][]_ MAY specify the _[Types][]_ of implied
_[Nested Member Types][]_ members in-line using `[]`.

```
array[number, string]
```

Indicates a `array` type structure MAY include distinct numbers or strings as values.

###### 3.2.1.3.1 Type Name
References the name of a type in _[Base Types][]_ or _[Named Types][]_. Some limitations apply (see
[Reserved Characters & Keywords][]).

##### 3.2.1.4 Type Attribute
Defines extra attributes associated with the implementation of a _[Type][]_.

- `required` - instance of this type is required
- `optional` - syntactic sugar for optional instance of this type
- `fixed` - indicates the structure and values are fixed for a data structure.

By default:

- If a _[Named Type][]_ or _[Member Type][]_ annotates its type as `fixed`, all child _[Nested Member Types][]_ inherit
the `fixed` attribute as well.
- An `array` based _[Named Type][]_ or _[Member Type][]_ MAY specify `fixed` to indicate the structure is a "fixed list" of
only the specified values.
- An `object` based _[Named Type][]_ or _[Member Type][]_ MAY specify `fixed` to indicate a "value object" where all
the properties MUST be present and the values of the properties MUST be the values specified in its
_[Property Member Types][]_.
- Individual _[Nested Member Types][]_ MAY override inherited behavior from a `fixed` parent type
by using an `optional` attribute and/or MAY indicate values are samples using a _[Variable Value][]_.

##### 3.2.1.5 Description
Describes a _[Member Type][]_ in-line.

_Description_ → `-` _Markdown-formatted text_

```
- name: Andrew (string) - <A Description>
```

###### 3.2.1.5.1 Block Description
Describes an _[Named Type][]_ or a _[Member Type][]_ with a nested (multi-line) text block.

_Block Description_ → _Markdown-formatted text_

Markdown lists that are part of a _[Block Description][]_ are considered part of the description.

```
- name: Andrew (string) - <A Description>

    An additional
    multi-line description

    - here
    - there
```

Note that `here` and `there` are NOT _[Member Types][]_ but rather are part of a Markdown list in the
_Block Description_.

##### 3.2.1.6 Samples
Defines alternate sample _[Values][]_ for _[Member Types][]_ as a nested Markdown list with (multi-line) text.

_Sample_ → `- Sample`

_Sample_ ⇒ _Markdown-formatted text_

```
- colors (array)
  - Sample
    red
```

##### 3.2.1.7 Validations
To Be Decided.

### 3.3 Nested Member Types
_[Named Types][]_ and _[Member Types][] directly, or indirectly, built from _[Structure Types][]_ MAY contain
_Nested Member Types_, which are defined using nested Markdown lists of allowed _[Member Types][]_.

A _Member Type_ that contains _Nested Member Types_ defines an inner, anonymous type that specifies the structure
of values of that particular member.

_Nested Member Types_ → _nested Markdown-formatted lists of_ _[Member Types][]_ | `-` _[Member Type List][]_

_Nested Member Types_ ⇒ _Nested Member Types_

In order to specify _[Nested Member Types][]_ after a _Block Description_, a _[Named Type][]_ or a [Member Type][]_
MUST use an appropriate _[Member Type Separator][]_ to indicate the end of the _Block Description_ and the beginning.

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

- Object Structures

    A _[Property Member Type][]_ without a _[Type Definition][]_ that contains _[Nested Member Types][]_ is by default
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

    A _[Member Type][]_ that includes a `array` _[Type Definition][]_ that contains _[Nested Member Types][]_ by default
    specifies an `array` type structure that MAY contain members of the specified _[Member Types][]_.

    ```
    -
    ```

- Enum Structures

## 4 Named Types
Defines a _[Custom Type][]_ based on an existing type using Markdown header tags.

_Named Type_ → `#` _[Type Name][]_ _[Type Definition][]_ _[opt]_

_Named Type_ ⇒ _[Block Descriptions][]_ _[opt]_

_Named Type_ ⇒ _[Nested Member Types][]_ _[opt]_

The above order of optional sections MUST be followed. _[Nested Member Types][]_ SHOULD be justified with the
related header.

```
# Person (object)
- first_name
- last_name
```

## 4 Named Type Members
Define _[Child Elements][]_ of a custom _[Compound Type][]_.

_Named Type Elements_ → `##` _[Elements Delimiter][]_

_Named Type Elements_ ⇒ _[opt]_ _[Child Elements][]_

The _Named Type Elements Delimiter_ SHOULD be nested one additional header level under
the associated _[Custom Named Type][]_.

- Without a _[Block Description][]_

    In-line _[Child Elements][]_ MAY be used.

    ```
    # Person (object) - Just and ordinary person
    - first_name
    - last_name
    ```
- With a _[Block Description][]_

    A _Named Type Elements_ section or an _[Element Delimiter][]_ MUST be used to define _[Child Elements][]_.

    ```
    # Person (object)
    Just an ordinary person

    ## Properties
    - first_name
    - last_name
    ```

## 5 Inheritance
Last element wins.

## 5.1 Mixins

## 5.2 One Of


## 6 Reserved Characters & Keywords
When using following characters or keywords in an _Instance Name_, Literal Value or _Type Name_ the name or literal
MUST be enclosed in backticks `` ` ``.

### 6.1 Characters

`:`, `(`,`)`, `<`, `>`, `{`, `}`, `[`, `]`, `_`, `*`, `-`, `+`, `` ` ``

## 6.2 Keywords

`Element`, `Elements`, `Property`, `Properties`, `Item`, `Items`, `Member`, `Members`, `Include`, `One of`

Note keywords are case insensitive.

### 6.3 Additional Keywords
Following keywords are reserved for future use:

`Trait`, `Traits`, `Parameter`, `Parameters`, `Attribute`, `Attributes`, `Filter`, `Validation`, `Choice`, `Choices`,
`Enumeration`, `Enum`, `Object`, `Array`

[RFC2119]: https://www.ietf.org/rfc/rfc2119
[Type]: #2-types
[Types]: #2-types
[Base Type]: #21-base-types
[Base Types]: #21-base-types
[Primitive Type]: #211-primitive-types
[Primitive Types]: #211-primitive-types
[Structure Type]: #212-structure-types
[Structure Types]: #212-structure-types
[Custom Type]: #22-custom-types
[Custom Types]: #22-custom-types

[Member Type]: #3-member-types
[Member Types]: #3-member-types
[Property Member Type]: #31-property-member-types
[Property Member Types]: #31-property-member-types
[Property Name]: #311-property-name
[Value Member Type]: #32-value-member-types
[Value Member Types]: #32-value-member-types
[Value Definition]: #321-value-definitions
[Value Definitions]: #321-value-definitions
[Value]: #3211-values
[Values]: #3211-values
[Literal Value]: #32111-literal-value
[Variable Value]: #32112-variable-value
[Type Definition]: #3212-type-definition
[Type Definitions]: #3212-type-definition
[Type Specification]: #3213-type-specification
[Type Specifications]: #3213-type-specification
[Type Name]: #32131-type-name
[Type Names]: #32131-type-name
[Type Attribute]: #3214-type-attribute
[Type Attributes]: #3214-type-attribute
[Description]: #3215-description
[Descriptions]: #3215-description
[Block Description]: #32151-block-description
[Block Descriptions]: #32151-block-description
[Sample]: #3216-samples
[Samples]: #3216-samples
[Validation]: #3217-validations
[Validations]: #3217-validations
[Nested Member Type]: #33-nested-member-types
[Nested Member Types]: #33-nested-member-types

[Named Type]: #4-named-types
[Named Types]: #4-named-types
[Inheritance]: #5-element-inheritance
[Reserved Characters & Keywords]: #6-reserved-characters--keywords
