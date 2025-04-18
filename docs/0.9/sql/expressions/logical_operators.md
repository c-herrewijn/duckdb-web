---
layout: docu
railroad: expressions/logical.js
redirect_from:
- docs/archive/0.9.2/sql/expressions/logical_operators
- docs/archive/0.9.1/sql/expressions/logical_operators
- docs/archive/0.9.0/sql/expressions/logical_operators
title: Logical Operators
---

<div id="rrdiagram"></div>

The following logical operators are available: `AND`, `OR` and `NOT`. SQL uses a three-valuad logic system with `true`, `false` and `NULL`. Note that logical operators involving `NULL` do not always evaluate to `NULL`. For example, `NULL AND false` will evaluate to `false`, and `NULL OR true` will evaluate to `true`. Below are the complete truth tables:


| `a` | `b` | `a AND b` | `a OR b` |
|:---|:---|:---|:---|
| `true` | `true` | `true` | `true` |
| `true` | `false` | `false` | `true` |
| `true` | `NULL` | `NULL` | `true` |
| `false` | `false` | `false` | `false` |
| `false` | `NULL` | `false` | `NULL` |
| `NULL` | `NULL` | `NULL` | `NULL`|


| `a` | `NOT a` |
|:---|:---|
| `true` | `false` |
| `false` | `true` |
| `NULL` | `NULL` |

The operators `AND` and `OR` are commutative, that is, you can switch the left and right operand without affecting the result.