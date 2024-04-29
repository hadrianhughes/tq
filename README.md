# tq

A language and command line tool for querying and manipulating tabular data (like CSV and TSV) --- inspired by [jq](https://jqlang.github.io/jq/).

## Concepts

- The language should be focused around dealing with tables. There must also be atomic types that tables will contain, but all of the language's operations should be mediated through tables. It should be capable of dealing with scalars by expressing them as 1x1 tables.

## Syntax

The grammar for the tq language in Extended Backus-Naur notation:

```ebnf
lowercase = "a" ... "z";

uppercase = "A" ... "Z";

digit = "0" ... "9";

integer = digit, { digit };

float = integer, ".", integer;

alphanumeric = lowercase | uppercase | digit;

string = "'", { ? any character ? }, "'";

Identifier = lowercase, { alphanumeric };

FunctionCall = Identifier, "(", Expression, ")";

OpSymbol = "+" | "-" | "*" | "/" | "^" | "|" | "=" | "|=";

Operation = (Expression, OpSymbol, Expression) | (Expression, OpSymbol) | (OpSymbol, Expression);

TableReference = lowercase | integer | (lowercase, integer);

TableRange = TableReference, ":", TableReference;

Selection = ".", (TableRange | TableReference);

Expression = Selection | Operation | FunctionCall | Identifier | string | integer | float;

Program = Expression;
```

## Examples

- Assign the value of a specific cell.

```
.a2 = 1
```

- Multiply the value of a specific cell by 2.

```
.c3 |= *2
```

- Take a 3x3 sub-table from the source table, then select the row with the value 'foo' in the first column.

```
.a1:c3 | select(.a == 'foo')
```

- Filter the `a` column to only include even numbers, then calculate their sum.

```
.a | select(% 2 | == 0) | sum
```
