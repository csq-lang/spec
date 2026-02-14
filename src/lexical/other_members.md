# Other lexical members

## Identifiers
Identifiers consist of:
- Any letter (or an underscore) present one time.
- Any letter, number or an underscore as all next characters repeated zero or more times.

>**Identifier examples**:
>```c
>myvar
>_default
>c9
>```

## Strings
Strings consist of:
- Either a single quote (`'`) or double quote (`"`), called the *delimiter*.
- Any character except the delimiter (being the first character, a single/double quote) repeated zero or more times.
- The delimiter once again.

Characters inside strings can be escaped with a backslash (`\`), meaning you can insert the delimiter of the string if you use a backslash (`\`) to escape it.

List of escaped characters:
- `\t`: tab
- `\n`: newline
- `\r`: carriage return
- `\"`, `\'`: double/single quote.
- `\\`: backslash.
- `\0`: null character.

>**String examples**:
>```c
>"this is a string"
>'this is a string too!'
>"this string has \"escaped\"\tcharacters!"
>```

## Digits
There are three types of digits:
- Decimal digits.
- Hexadecimal digits.
- Binary digits.

### Decimal digits
They consist of:
- Any character from `0` to `9` repeated one or more times.
- Optionally, a period (`.`) followed by any character from `0` to `9` repeated one or more times.

### Hexadecimal digits
They consist of:
- The prefix `0x`, which **can't change**.
- Any character from `0` to `9` and `a` to `f` (case insensitive) repeated one or more times.

### Binary digits
They consist of:
- The prefix `0b`, which **can't change**.
- Either `0` or `1` repeated one or more times.

>**Digit examples**:
>```c
>5
>3.14
>0x56F
>0b1011
>```

## Operators, punctuation

Operators can't be matched by a single expression, as there is a static list of possible operators/punctuation.

All operators supported in C&sup2;:
- `+` 
- `+=`
- `++`
- `-`
- `-=`
- `->`
- `*`
- `*=`
- `/`
- `//`
- `/=`
- `%`
- `^`
- `=`
- `==`
- `!`
- `!=`
- `<`
- `<=`
- `>`
- `>=`
- `&`
- `&&`
- `|`
- `||`
- `?`

All punctuation supported in C&sup2;:
- `.`
- `..`
- `...`
- `(`
- `)`
- `{`
- `}`
- `[`
- `]`
- `:`
- `;`
- `,`
- `#`
