---
layout: docu
railroad: expressions/comparison.js
title: Comparisons
---

## Comparison Operators

<div id="rrdiagram2"></div>

The table below shows the standard comparison operators.
Whenever either of the input arguments is `NULL`, the output of the comparison is `NULL`.


| Operator | Description | Example | Result |
|:---|:---|:---|:---|
| `<` | less than | `2 < 3` | `true` |
| `>` | greater than | `2 > 3` | `false` |
| `<=` | less than or equal to | `2 <= 3` | `true` |
| `>=` | greater than or equal to | `4 >= NULL` | `NULL` |
| `=` | equal | `NULL = NULL` | `NULL` |
| `<>` or `!=` | not equal | `2 <> 2` | `false` |

The table below shows the standard distinction operators.
These operators treat `NULL` values as equal.


| Operator | Description | Example | Result |
|:---|:---|:---|:-|
| `IS DISTINCT FROM` | not equal, including `NULL` | `2 IS DISTINCT FROM NULL` | `true` |
| `IS NOT DISTINCT FROM` | equal, including `NULL` | `NULL IS NOT DISTINCT FROM NULL` | `true` |

## `BETWEEN` and `IS [NOT] NULL`

<div id="rrdiagram1"></div>

Besides the standard comparison operators there are also the `BETWEEN` and `IS (NOT) NULL` operators. These behave much like operators, but have special syntax mandated by the SQL standard. They are shown in the table below.

Note that `BETWEEN` and `NOT BETWEEN` are only equivalent to the examples below in the cases where both `a`, `x` and `y` are of the same type, as `BETWEEN` will cast all of its inputs to the same type.


| Predicate | Description |
|:---|:---|
| `a BETWEEN x AND y` | equivalent to `x <= a AND a <= y` |
| `a NOT BETWEEN x AND y` | equivalent to `x > a OR a > y` |
| `expression IS NULL` | `true` if expression is `NULL`, `false` otherwise |
| `expression ISNULL` | alias for `IS NULL` (non-standard) |
| `expression IS NOT NULL` | `false` if expression is `NULL`, `true` otherwise |
| `expression NOTNULL` | alias for `IS NOT NULL` (non-standard) |

> For the expression `BETWEEN x AND y`, `x` is used as the lower bound and `y` is used as the upper bound. Therefore, if `x > y`, the result will always be `false`.