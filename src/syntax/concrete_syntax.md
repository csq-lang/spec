# Grammar
Also known as concrete syntax.

|**Usage**|**Notation**
:-----:|:-----:
definition |<code>**=**</code>
concatenation |<code>,</code>
termination |<code>;</code>
alternation |<code>&#124;</code>
optional |<code>[ ... ]</code>
repetition |<code>{ ... }</code>
grouping |<code>( ... )</code>
terminal string |<code>" ... "</code>
terminal string |<code>' ... '</code>
comment |<code>(* ... *)</code>
special sequence |<code>? ... ?</code>
exception |<code>-</code>

> [!NOTE]
> Table from [this GitHub gist](https://gist.github.com/refs/e6e84057eb047ee5f306b76e13f76d1e).

> [!NOTE]
> New version of the grammar (not deprecated).

```bnf
program                ::= { external_declaration } ;

external_declaration   ::= function_definition
                         | declaration
                         | namespace_definition
                         | class_definition
                         | type_definition
                         ;

(* new: type definition *)
type_definition        ::= "type" identifier "=" type_definition_rhs ";" ;
type_definition_rhs    ::= "struct" "{" { struct_member } "}"
                         | type_specifier
                         ;
struct_member          ::= declaration_specifiers init_declarator_list? ";" ;

declaration            ::= declaration_specifiers init_declarator_list? ";" ;
declaration_specifiers ::= storage_class_specifier* type_specifier type_qualifier* ;
storage_class_specifier::= "extern" | "static" ;
type_specifier         ::= "void" | "int" | "uint" | "short" | "ushort" | "long" | "ulong"
                         | "float" | "double" | "quad" | "bool" | "char" | "uchar"
                         | identifier
                         | template_type
                         ;
template_type          ::= identifier "<" template_arg_list ">" ;
template_arg_list      ::= template_arg { "," template_arg } ;
template_arg           ::= type_specifier | expression ;

type_qualifier         ::= "const" ;

init_declarator_list   ::= init_declarator { "," init_declarator } ;
init_declarator        ::= declarator [ "=" initializer ] ;
declarator             ::= pointer? direct_declarator ;
pointer                ::= "*" type_qualifier* pointer? | "&" ;
direct_declarator      ::= identifier
                         | "(" declarator ")"
                         | direct_declarator "[" constant_expression? "]"
                         | direct_declarator "(" parameter_type_list? ")"
                         ;

parameter_type_list    ::= parameter_list [ "," "..." ] ;
parameter_list         ::= parameter_declaration { "," parameter_declaration } ;
parameter_declaration  ::= declaration_specifiers declarator? ;

initializer            ::= assignment_expression | "{" initializer_list "}" ;
initializer_list       ::= initializer { "," initializer } ;

function_definition    ::= declaration_specifiers declarator compound_statement ;

namespace_definition   ::= "namespace" identifier ;

class_definition       ::= class_head "{" { class_member } "}" ";" ;
class_head             ::= ("class" | "struct") identifier? base_clause? ;
base_clause            ::= ":" base_specifier_list ;
base_specifier_list    ::= base_specifier { "," base_specifier } ;
base_specifier         ::= ("public" | "protected" | "private")? identifier ;

class_member           ::= declaration
                         | function_definition
                         | access_specifier ":" 
                         ;
access_specifier       ::= "public" | "protected" | "private" ;

statement              ::= compound_statement
                         | expression_statement
                         | selection_statement
                         | iteration_statement
                         ;

compound_statement     ::= "{" { declaration | statement } "}" ;
expression_statement   ::= expression? ";" ;

selection_statement    ::= "if" "(" expression ")" statement [ "else" statement ]
                         | "switch" "(" expression ")" statement
                         ;

iteration_statement    ::= "while" "(" expression ")" statement
                         | "do" statement "while" "(" expression ")" ";"
                         | "for" "(" for_init_statement expression? ";" expression? ")" statement
                         ;
for_init_statement     ::= expression_statement | declaration ;

expression             ::= assignment_expression { "," assignment_expression } ;

assignment_expression  ::= conditional_expression
                         | unary_expression assignment_operator assignment_expression
                         ;
assignment_operator    ::= "=" | "+=" | "-=" | "*=" | "/=" | "%=" ;

conditional_expression ::= logical_or_expression [ "?" expression ":" conditional_expression ] ;

logical_or_expression  ::= logical_and_expression { "||" logical_and_expression } ;
logical_and_expression ::= inclusive_or_expression { "&&" inclusive_or_expression } ;
inclusive_or_expression::= exclusive_or_expression { "|" exclusive_or_expression } ;
exclusive_or_expression::= and_expression { "^" and_expression } ;
and_expression         ::= equality_expression { "&" equality_expression } ;
equality_expression    ::= relational_expression { ( "==" | "!=" ) relational_expression } ;
relational_expression  ::= shift_expression { ( "<" | ">" | "<=" | ">=" ) shift_expression } ;
shift_expression       ::= additive_expression { ( "<<" | ">>" ) additive_expression } ;
additive_expression    ::= multiplicative_expression { ( "+" | "-" ) multiplicative_expression } ;
multiplicative_expression ::= cast_expression { ( "*" | "/" | "%" ) cast_expression } ;

cast_expression        ::= unary_expression | "(" type_name ")" cast_expression ;

unary_expression       ::= postfix_expression
                         | "++" unary_expression
                         | "--" unary_expression
                         | unary_operator cast_expression
                         | "sizeof" unary_expression
                         ;
unary_operator         ::= "&" | "*" | "+" | "-" | "~" | "!" ;

postfix_expression     ::= primary_expression
                         | postfix_expression "[" expression "]"
                         | postfix_expression "(" argument_expression_list? ")"
                         | postfix_expression "." identifier
                         | postfix_expression "::" identifier
                         | postfix_expression "++"
                         | postfix_expression "--"
                         ;

primary_expression     ::= identifier
                         | constant
                         | string_literal
                         | "(" expression ")"
                         ;

argument_expression_list ::= assignment_expression { "," assignment_expression } ;

constant               ::= number | char_literal ;
constant_expression    ::= conditional_expression ;

type_name              ::= specifier_qualifier_list abstract_declarator? ;
specifier_qualifier_list ::= type_specifier type_qualifier* ;

abstract_declarator    ::= pointer direct_abstract_declarator? | direct_abstract_declarator ;
direct_abstract_declarator ::= "(" abstract_declarator ")"
                            | "[" constant_expression? "]" direct_abstract_declarator?
                            | "(" parameter_type_list? ")" direct_abstract_declarator?
                            ;

translation_unit       ::= program ;
```
