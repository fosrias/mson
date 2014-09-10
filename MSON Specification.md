# MSON Specification
Markdown Syntax for Object Notation (MSON) is a plain-text syntax for the description and validation of data structures.

In MSON, data structures are recursively described by a combination of MSON _[Elements][]_, typically written in-line as
Markdown lists:

    - <Base Element>                  - name (string)
    - <Compound Element>                - item
      - <Base Element>                  - price: 5 (number)
      - <Compound Element>                - order (object)
        - <Base Element>                  - id (number)
        - <Base Element>                  - description (string)
    - <Custom Element>                  - customer (Customer)

## 1 How to Read the Grammar
- An arrow (→) is used to mark grammar productions that can be read as "consists of|consists of a(n)"
- A double arrow (⇒) is used to mark grammar productions that can be read as "contains|contains a(n)"
- _Italic_ text indicates syntactic categories
- A `code span` indicates literal words and punctuations
- Pipe Characters (|) separate alternative grammar productions
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

### 2.1 Named Types
MSON defines a number of base primitive and compound types and allows creating custom types.

#### 2.1.1 Primitive Types
- `boolean` or `Boolean`
- `string`  or `String`
- `number`  or `Number`

#### 2.1.2 Compound Types
- `object` or `Object`
- `array` or `Array`
- `enumeration` or `enum` or `Enumeration` or `Enum`
- `tuple` or `Tuple`

#### 2.1.3 User-defined, Custom Types
Users may extend these base types to define new _[Custom Named Types][]_.

## 3 Elements
An _Element_ is a generic building block that specifies the structure, types, allowed and sample values and text
descriptions of diverse data structures.

_Element_ → _[Property Name][]_ `:` _[opt]_  _[Value Type][]_ _[opt]_ | _[Value Type][]_

The optional `:` is only applicable when a _[Value Type][]_ is present and includes a _[Value][]_.

```
- name: Andrew (string) - A name of someone
```

### 3.1 Base Elements
MSON defines three _Base Elements_ including _[Property][]_, _[Member][]_, and _[Mixin Elements][]_.

#### 3.1.1 Property Element
Defines the structure of individual properties in an `object` type structure.

_Property Element_ → _[Property Name][]_ `:` _[opt]_  _[Value Type][]_ _[opt]_

```
- count: 1 (number) - A count of something
```

The optional `:` is only applicable in the case where a _[Value Type][]_ is present and includes a _[Value][]_.

##### 3.1.1.1 Property Name
Defines the name of a property in an `object` type structure.

_Property Name_ → _[Literal Value][]_ | _[Variable Value][]_

```
- customer (object)
```

Where a _[Property Element][]_ with _Property Name_ "customer" is defined that is an `object` type structure.

```
- *rel* (string)
```
When a _Property Name_ is a _[Variable Value][]_, it indicates a property MAY have an arbitrary name and the
specified _Property Name_ is then a sample. In the prior example, a _Property Element_ of a `string` type is defined
with an arbitrary name and a sample value of "rel".

#### 3.1.2 Member Element
Defines the possible items in an `array` type structure or members in an `enumeration` type structure.

_Member Element_ → _[Value Type][]_

```
- colors (array)
  - red
  - green
```

Where a `colors` `array` type structure is specified that MAY have any arbitrary `string` (the default _[Type][]_ of
_[Child Elements][]_ without a specified _[Value Type][]_) as items, with "red" and "green" provided as sample values.

#### 3.1.3 Mixin Element
A Mixin Element is a special type of _[Element][]_ that adds _[Elements][]_ from one _[Compound Element][]_ to another.
The source _[Compound Element][]_ MUST be defined using a _[Custom Named Type][]_ and be of the same base
_[Compound Type][]_ as the parent _[Compound Element][]_.

_Mixin Element_ → `- Include` _[Custom Type Name][]_

```
- person (object)
  - name
  - Include Address
```

See _[Element Inheritance][]_ to understand order considerations when _Mixin Elements_ contain _[Property Elements][]_
with the same _Property Name_ as sibling _[Elements][]_ under a parent _[Compound Element][]_.

### 3.2 Compound Elements
MSON defines four _Compound Element_ including _[Object][]_, _[Array][]_, _[Enumeration][]_ and
_[Tuple Elements]_, which are used to describe data structures that contain child _[Base][]_, _[Compound][]_, and/or
_[Custom Elements][]_.

_Compound Element_ → _[Object Element][]_ | _[Array Element][]_ | _[Enumeration Element][]_ | _[Tuple Element][]_

_Compound Element_ ⇒ _[opt]_  _[Child Elements][]_

```
- person
  - first_name
  - last_name
```

Where `person` is an _[Compound Element][] for an `object` type structure with _[Child Elements][]_ `first_name`
and `last_name`.

#### 3.2.1 Child Element
Defines a child _[Element][]_ in a _[Compound Element][]_.

_Child Element_ →  _[Base Element][]_ | _[Compound Element][]_ | _[Custom Element][]_

_Child Elements_ MUST be specified in one of the following ways:

  - In-line as a nested Markdown list directly under a _[Compound Element][]_

  ```
  - person (object)
    - first_ name
    - last_name
  ```

  - In-line as a nested Markdown list directly under an _[Element Delimiter][]_ in a Markdown List

  ```
  - cities (array)
    This is a multi-line additional description.
    When this type of description is used, child
    elements must nest under an Element Delimiter.

    - Not an item
    - But a list in the description text

    - `Items`
      - NY
      - SF
  ```

  Where `Items` is an _[Element Delimiter][]_ for an `array` type structure's _Child Element's_ `NY` and `SF`.

##### 3.2.1.1 Element Delimiters
MSON defines three _Element Delimiters_ including _[Properties][]_, _[Items][]_, and _[Members Delimiters][]_ that can
be used to indicate lists of nested _[Child Elements]_.

_Elements Delimiter_  → _[Properties Delimiter][]_ | _[Items Delimiter][]_ | _[Members Delimiter][]_

_Elements Delimiter_ ⇒ _[Child Elements][]_

###### 3.2.1.1.1 Properties Delimiter
Defines a group of child _[Property Elements]_ associated with `object` type structures.

```
- Properties
  - first_ name
  - last_name
```

###### 3.2.1.1.2 Items Delimiter
Defines a group of child _[Member Elements]_ associated with `array` type structures.

_Items Delimiter_ → `Items`

```
- Items
  - red
  - green
```

###### 3.2.1.1.3 Members Delimiter
Defines a group of child _[Member Elements]_ associated with `enumeration` or `tuple` type structures.

_Members Delimiter_ → `Members`

```
- Members
  - 0
  - 1
  - 1
  - 2
```

#### 3.2.2 Object Element
Defines an `object` type structure.

_Object Element_ → _[Property Name][]_ `:` _[opt]_  _[Value Type][]_ _[opt]_ | _[Value Type][]_

_Object Element_ ⇒ _[Property Elements][]_ | _[Mixin Elements][]_ | _[One Of Elements][]_

_Object Element_ ⇒ _[Properties Delimiter][]_

```
- person
    - name
    - address (object)
      - city
      - One Of
        - state
        - province
```

By default:

- Any top-level set of in-line _[Elements][]_ collectively define an implied parent _Object Element_.
- A top-level, in-line _[Element][]_ with only a _[Property Name][]_, which also contains _[Child Elements][]_, implies
an `object` type.
- An _[Element][]_ with only a _[Property Name][]_ or a _[Property Name][]_ and a _[Value][]_ implies a `string` type.

Thus, `person` is both an _Object Element_ itself and the only member of an implied MSON _Object Element_, while `city`
and `state` are both _[Property Elements][]_ of a `string` type.

##### 3.2.2.1 One Of Element
Defines alternate, exclusive possibilities for _[Property Elements][]_ in an _[Object Element][]_.

_One Of Element_ → `- One Of`

_One Of Element_ ⇒ _[Property Elements][]_ | _[Mixin Elements][]_

```
_ person
  - One Of
    - first_name
    - given_name
  - last_name
```

Where `person` MAY either have a `first_name` or a `given_name` property, but MUST NOT have both simultaneously and
MAY have a `last_name`.

#### 3.2.3 Array Element
Defines allowed types/values of items in an `array` type structure.

_Array Element_ ⇒ _[opt]_ _[Member Elements][]_ | _[Mixin Elements][]_

```
- list (array)
  - green (string)
  - (number)
```

Where `list` is both  a _[Property Element][]_ with a _[Property Name][]_ "list" and an _Array Element_ defining an
array data structure that MAY contain any `string`, with a _[Variable Value][]_ of "green", or any `number` as possible
items.

#### 3.2.4 Enumeration Element
Defines the members of an `enumeration` type structure.

_Enumeration Element_ ⇒ _[Member Elements][]_ | _[Mixin Elements][]_

```
- rel (enum)
  - b
  - (object)
  - *1* (number)
```

By default:

- When _[Member Elements][]_ of an _Enumeration Element_ contain a _[Literal Value][]_, it is a fully-qualified
value of the enumeration.
- When _[Member Elements][]_ of an _Enumeration Element_ do NOT contain a _[Value][]_ or only contain a
_[Variable Value][]_, they define exclusive anonymous allowed types as fully-qualified values of the enumeration.
- A _[Variable Value][]_ in an enumeration indicates a sample value for an associated anonymous type member.


Thus, the _Enumeration Element_ `rel` will either be `string` of value "b" (the default behavior of _[Child Elements][]_
without a specified _[Value Type][]_) or some arbitrary `object` or some arbitrary `number` with a sample value of 1.

##### 3.2.4.1 Enumerations As Constraints
Anonymous _[Enumeration Elements][]_ (without a _[Value][]_ specified in its _[Value Type][]_) indicate
constraints on the members of various _[Elements][]_. This includes:


Indicate items in an `array` data structure MUST have a particular value.

```
- colors (array)
  - (enum)
    - red
    - green
    - blue
```

Specifies an array of colors MAY only have items with values of "red", "green" or "blue".

Note the difference between specifying a list of sample values for an array:

```
- colors (array)
  - red
  - green
  - blue
```

Which indicates an array MAY have any string for the value of one of its items with sample values of "red",
"green" and "blue".

#### 3.2.5 Tuple Element
Defines a `n-tuple` data structure based on the number and order of child _[Member Elements][]_.

_Tuple Element_ ⇒ _[Member Elements][]_ | _[Mixin Elements][]_

```
- value (tuple)
  - 1 (number)
  - a (string)
  - (object)
```

Where `value` defines a "triple" data structure which can have any `number` followed by any `string` followed by
any `object`.

### 3.3 Custom Elements
Defines _[Elements][]_ based on user-defined, _[Custom Named Types][]_ that are referenced in the associated
_[Value Type][]_. _Custom Elements_ inherit all _[Child Elements][]_ defined in the specified _[Custom Named Type][]_.

```
- person (Person)
```

### 3.4 Value Types
Every _[Element][]_ specifies type information associated with values in some structure or element in a structure. This
_Value Type_ information may include literal and sample _[Values][]_ of associate type instances, and
_[Type Definitions]_, including _[Type Attributes][]_ and _[Descriptions][]_.

_Value Type_ → _[Value][]_ _[opt]_ _[Type Definition][]_ _[opt]_ `-` _[opt]_ _[Description][]_

_Value Type_ ⇒ _[Block Description][]_ _[opt]_

_Value Type_ ⇒ _[Validations][]_ _[opt]_

_Value Types_ of the `object` _[Compound Type][]_ MUST NOT specify a _[Value][]_. The optional `-` is only applicable
in the case where an in-line _[Description][]_ is provided.

```
5, 6 (array[number], required, fixed) - Some description
```

Which indicates a _Value Type_ for a fixed array that only contains the two numbers `5` and `6` as items. See `fixed`
_[Type Attribute][]_ for more information.

#### 3.4.1 Values
Defines either sample or actual value in an _[Element][]_ based on the associated _[Type][]_ of a _[Value Type][]_.

_Value_ → _[Literal Value][]_ | _[Variable Value][]_ | _Values List_

_Values List_ → _Value_ | _Value_`,` _Values List_

```
green, red
```

##### 3.4.1.1 Literal Value
Literal value of a type instance. Some limitations apply (see [Reserved Characters & Keywords][]).

```
5
```

A _[Value][]_ in a _[Value Type][]_ with an unspecified type MAY imply the type of the _[Element][]_, for example
`number` in this example.

##### 3.4.1.2 Variable Value
Defines a _Value_ that is not concrete and is used to indicate variable _[Property Names][]_ or sample values.

_Variable Value_ → `*`_[Literal Value][]_`*` | `_`_[Literal Value][]_`_` |`*`_[Value Type][]_`*` | `_`_[Value Type][]_`_`

```
*rel*
```

In the case of specifying a variable _[Property Name][]_, the _Variable Value_ MAY specify a _[Custom Named Type][]_
that MUST inherit from a `string` type, e.g. to specify a pattern for the variable value.

```
_rel (Custom String)_ (object)
```

Where `rel` is a sample value for the _[Property Name][]_ of a _[Property Element][]_ that specifies an `object` type
structure.

#### 3.4.2 Type Definition
Explicitly specifies the type of an value in an MSON instance.

_Type Definition_ → `(`_[Type Specification][]_ _[opt]_ `,` _[opt]_ _Type Attributes List_ _[opt]_`)`

_Type Attributes List_ → _[Type Attribute][]_ | _[Type Attribute][]_`,` _Type Attributes List_

A _Type Definition_ MUST separate multiple items with a `,`.

```
(enum, optional)
```

#### 3.4.3 Type Specification

_Type Specification_ → _[Type Name][]_ | _[Type Name][]_`[`_Type Name List_`]`


_Type Name List_ →  _[Type Name][]_ | _[Type Name][]_`,` _Type Name List_

`array` and `enumeration` _[Compound Elements][]_ MAY specify the _[Types][]_ of their members in-line using `[]` to
 indicate the list of types.

```
array[number, string]
```

#### 3.4.4 Type Attribute
Defines extra attributes associated with the implementation of a type in an _[Element][]_.

- `required` - instance of this type is required
- `optional` - syntactic sugar for optional instance of this type
- ` ` - indicates the structure and values of an instance are fixed based on the _[Element][]_ MSON.

By default:

- If a parent _[Compound Element][]_ annotates its type as `fixed`, all child _[Elements][]_ inherit the `fixed`
attribute as well.
- An _[Array Element][]_ MAY specify `fixed` to indicate the structure is a "fixed list" of only the specified
values.
- An _[Object Element][]_ MAY specify `fixed` to indicate a "value object" where all the properties MUST be
present and the values of the properties MUST be the values in the _[Property Elements]_.
- Individual child _[Property Elements][]_ may override inherited behavior from a `fixed` parent _[Object Element][]_
by using an `optional` attribute.
- Individual child _[Member Elements][]_ with a `fixed` parent _[Array Element][]_
MAY indicate values are samples using a _[Variable Value][]_.

#### 3.4.5 Type Name
Name of a type including built-in named types. Some limitations apply (see [Reserved Characters & Keywords][]).

#### 3.4.6 Description
Describes an _[Element][]_ in-line.

_Description_ → `-` _Markdown-formatted text_

```
- name: Andrew (string) - <A Description>
```

##### 3.4.6.1 Block Description
Describes an _[Element][]_ with a nested (multi-line) text block.

_Block Description_ → _Markdown-formatted text_

```
- name: Andrew (string) - <A Description>

    An additional
    multi-line description

    - here
    - there
```

Note that `here` and `there` are NOT _[Elements][]_ but rather are part of a Markdown list in the
_Block Description_. In order to specify _[Child Elements][]_ after a _Block Description_, a
_[Compound Element][]_ must use an appropriate _[Element Delimiter][]_ to indicate the end of the
_Block Description_.

#### 3.4.7 Validations
To Be Decided.

## 4 Custom Named Types
Defines a new _[Named Type][]_ based on an existing type using Markdown header tags.

_Custom Named Type_ → `#` _[Custom Type Name][]_ _[Type Definition][]_

_Custom Named Type_ ⇒ _[Block Descriptions][]_ _[opt]_

_Custom Named Type_ ⇒ _[Child Elements][]_ _[opt]_

_Custom Named Type_ ⇒ _[Named Type Elements][]_ _[opt]_

The above order of optional sections MUST be followed.

```
# Person (object)
- first_name
- last_name
```

TODO: How to "name" and re-use "inline" elements.

### 4.1 Custom Type Name
Defines the name of a custom _[Type][]_.

_Custom Type Name_ → _[Literal Value][]_

### 4.2 Named Type Elements
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

## 5 Element Inheritance
Last element wins.

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

[Named Type]: #21-named-types
[Primitive Type]: #211-primitive-types
[Primitive Types]: #211-primitive-types
[Compound Type]: #212-compound-types
[Compound Types]: #212-compound-types

[Element]: #3-elements
[Elements]: #3-elements

[Base]: #31-base-elements
[Base Element]: #31-base-elements
[Base Elements]: #31-base-elements
[Property]: #311-property-element
[Property Element]: #311-property-element
[Property Elements]: #311-property-element
[Property Name]: #3111-property-name
[Property Names]: #3111-property-name
[Member]: #312-member-element
[Member Element]: #312-member-element
[Member Elements]: #312-member-element
[Mixin Element]: #313-mixin-element
[Mixin Elements]: #313-mixin-element

[Compound]: #32-compound-elements
[Compound Element]: #32-compound-element
[Compound Elements]: #32-compound-element
[Child Element]: #321-child-element
[Child Elements]: #321-child-element
[Element Delimiter]: #3211-element-delimiters
[Elements Delimiter]: #3211-element-delimiters
[Element Delimiters]: #3211-element-delimiters
[Properties]: #32111-properties-delimiter
[Properties Delimiter]: #32111-properties-delimiter
[Items]: #32112-items-delimiter
[Items Delimiter]: #32112-items-delimiter
[Members]: #32113-members-delimiter
[Members Delimiter]: #32113-members-delimiter
[Members Delimiters]: #32113-members-delimiter
[Object Element]: #322-object-element
[Object Elements]: #322-object-element
[One Of Element]: #3221-one-of-element
[One Of Elements]: #3221-one-of-element
[Array Element]: #323-array-element
[Array Elements]: #323-array-element
[Enumeration Element]: #324-enumeration-element
[Enumeration Elements]: #324-enumeration-element
[Tuple Element]: #325-tuple-element
[Tuple Elements]: #325-tuple-element

[Custom Element]: #33-custom-elements
[Custom Elements]: #33-custom-elements

[Value Type]: #34-value-types
[Value Types]: #34-value-types
[Value]: #341-values
[Values]: #341-values
[Literal Value]: #3411-literal-value
[Variable Value]: #3412-variable-value
[Type Definition]: #342-type-definition
[Type Definitions]: #342-type-definition
[Type Specification]: #343-type-specification
[Type Specifications]: #343-type-specification
[Type Attribute]: #344-type-attribute
[Type Attributes]: #344-type-attribute
[Type Name]: #345-type-name
[Type Names]: #345-type-name
[Description]: #346-description
[Descriptions]: #346-description
[Block Description]: #3461-block-description
[Block Descriptions]: #3461-block-description
[Validations]: #347-validations

[Custom Named Type]: #4-custom-named-types
[Custom Named Types]: #4-custom-named-types
[Custom Type Name]: #41-custom-type-name
[Named Type Element]: #42-named-type-elements
[Named Type Elements]: #42-named-type-elements

[Element Inheritance]: #5-element-inheritance

[Reserved Characters & Keywords]: #6-reserved-characters--keywords
