# Lexer
The lexer has the job of turning your source into a list of [tokens](./tokens.md).

Given this source:
```csq
func main() -> int
{
  puts("Hello, World!")
  return 0
}
```

The lexer will generate this token list:
> format: `TOKEN_TYPE`, `LEXEME`
```c
TOKEN_KEYWORD_FUNC, "func";
TOKEN_IDENTIFIER, "main";
TOKEN_OPEN_PAREN, "(";
TOKEN_CLOSE_PAREN, ")";
TOKEN_ARROW, "->";
TOKEN_KEYWORD_INT, "int";
TOKEN_OPEN_BRACE, "{";
TOKEN_IDENTIFIER, "puts";
TOKEN_OPEN_PAREN, "(";
TOKEN_STRING, "Hello, World!";
TOKEN_CLOSE_PAREN, ")";
TOKEN_KEYWORD_RETURN, "return";
TOKEN_NUMBER, "0";
TOKEN_CLOSE_BRACE, "}";
```

This output will then be fed to the [parser](../parser/index.md).

## The `csq_lexer` type
The `csq_lexer` type represents the lexer internally. It serves as storage for the lexer state and for position tracking (line and column, for error reports).

It contains:
- `buffer`: entire source file buffer. 
- `start`: start of the current token being scanned.
- `current`: current position in buffer.
- `line`: current line number (1-indexed).
- `column`: current column number (1-indexed).
- `path`: path to source file for error messages.
- `diag`: diagnostic reporter for error handling.

> **Full definition**:
>```c
>typedef struct csq_lexer {
>    const char* buffer;
>    const char* start;
>    const char* current;
>    size_t line;
>    size_t column;
>    const char* path;
>    DiagReporter* diag;
>} csq_lexer;
>```

## Main lexing process
To lex, C&sup2; first creates the lexer using the [`lexer_create`](https://github.com/csq-lang/csquared/blob/main/src/csquare/lexer.c#L28) function

C&sup2; uses a state table to lex, meaning that to start lexing a call to [`lexer_next`](https://github.com/csq-lang/csquared/blob/main/src/csquare/lexer.c#L454) is made.

`lexer_next`:
1. Checks for whitespaces, which are then skipped.
2. Updates `lexer->start` to `lexer->current`.
3. If the end of the buffer was reached, an EOF (End of file) token is returned.
4. The lexer gets current lex state. The lex state is of type `csq_lexstate`, type defined as `csq_token (*csq_lexstate)(csq_lexer*))` which acts as a function pointer for lexer state handlers.
5. `state(lexer)` is returned, as `state` is a function pointer.
    - If there is no state (the state is `NULL`), then an error token is returned.

`state(lexer)` will then return a token. The way `state` is retrieved is through the `get_lex_state` function, which checks the state table and returns the correct lexer state handler depending on the lexer's current character.

For example, if the character is a digit (=`0` to `9`), then `lex_number` is returned, the handler for `TOKEN_NUMBER` tokens.

## Lexer state handlers
There are 6 lexer state handlers: `lex_whitespace`, `lex_identifier`, `lex_number`, `lex_string`, `lex_tag` and `lex_operator`.

`lex_whitespace` just advances to the next token.

`lex_identifier` will first advance character while the current one is an alphanumeric character (`a-z`, `A-Z` or `0-9`) or an underscore, then it will declare the *type* variable, which is obtained through the `check_keyword` function, which either returns a `TOKEN_KEYWORD_*` type 
or `TOKEN_IDENTIFIER`, if the identifier isn't a keyword. Finally, the function will check correct format of the identifier and, if everything is valid, it will return a new `TOKEN_IDENTIFIER` token.

`lex_number` will first check the base of the digit: default is `10`, but if any prefix that modifies it (`0x`/`0X`, `0b`/`0B`, `0o`/`0O`) is found, then it will change to the correct base (16, 2 and 8, respectively). 
After retrieving the base, it will lex the digit, check correct format and finally return a new `TOKEN_NUMBER` token.

`lex_string` will first set the variable `quote` to either a double or single quote, depending on how the string starts. Then, it will enter a while loop, where while the current character isn't the quote and there is a character after it, it will add new characters to the string. 
To add characters, it will first check for [escaped characters](/lexical/other_members.html#strings), and if it doesn't find any, it will simply add the current one. It will finally return a new `TOKEN_STRING` token.

`lex_tag` is very similar to `lex_string`, but it will first check for the `@` prefix.

`lex_operator` will use a switch statement to check what operator was detected: it will first check for the first character, and if there are two or three character operators/puncuation that start equally, then it will check if it continues. It will finally return a new `TOKEN_*` token (every operator/puncuation has its own `csq_tokentype`).

## The lex state table
This table is used to determine what handler is used for the current token.
```c
void initialize_state_table(void) {
  state_table[' '] = lex_whitespace;
  state_table['\t'] = lex_whitespace;
  state_table['\n'] = lex_whitespace;
  state_table['\r'] = lex_whitespace;
  state_table['\v'] = lex_whitespace;
  state_table['\f'] = lex_whitespace;

  for (char c = 'a'; c <= 'z'; c++)
    state_table[(unsigned char)c] = lex_identifier;

  for (char c = 'A'; c <= 'Z'; c++)
    state_table[(unsigned char)c] = lex_identifier;

  state_table['_'] = lex_identifier;

  for (char c = '0'; c <= '9'; c++)
    state_table[(unsigned char)c] = lex_number;

  state_table['"'] = lex_string;
  state_table['\''] = lex_string;

  state_table['@'] = lex_tag;

  state_table['+'] = lex_operator;
  state_table['-'] = lex_operator;
  state_table['*'] = lex_operator;
  state_table['/'] = lex_operator;
  state_table['%'] = lex_operator;
  state_table['^'] = lex_operator;
  state_table['='] = lex_operator;
  state_table['!'] = lex_operator;
  state_table['<'] = lex_operator;
  state_table['>'] = lex_operator;
  state_table['&'] = lex_operator;
  state_table['|'] = lex_operator;
  state_table['.'] = lex_operator;
  state_table['('] = lex_operator;
  state_table[')'] = lex_operator;
  state_table['{'] = lex_operator;
  state_table['}'] = lex_operator;
  state_table['['] = lex_operator;
  state_table[']'] = lex_operator;
  state_table[':'] = lex_operator;
  state_table[';'] = lex_operator;
  state_table[','] = lex_operator;
  state_table['#'] = lex_operator;
  state_table['?'] = lex_operator;

  state_table['\0'] = NULL;
}
```
