# SRFI nnn: Make and break inexact reals

by Firstname Lastname, Another Person, Third Person

## Status

Early Draft

## Abstract

This SRFI provides procedures to make inexact reals (i.e.
floating-point numbers) from integer or string components, and to
break down reals into their components. This is useful when writing
readers and writers for number syntaxes other than Scheme's own.

## Issues

## Rationale

The details of float building are difficult to get right, and casual
attempts to implement it on the basis of high school mathematics are
likely to give results that are subtly different from a Scheme
implementation's native `string->number` procedure.

## Specification

### Make reals

The procedures in this section return an inexact real number built
from components using the same method as the Scheme implementation's
version of `string->number` does.

The `precision` argument says what floating-point precision to use for
the number being built.

The standard values for `precision` are the lowercase letters `e`,
`s`, `f`, `d`, `l`. The letter `e` means to use the underlying Scheme
implementation's default precision (which may be configurable in a
non-standard way). The other letters stand for a short, single,
double, and long float, respectively. These precisions are explained
in R7RS section 6.2.5. (_Syntax of numerical constants_). The standard
letters may be given as a character object, a one-character string, or
a symbol whose name is a one-character string.

There may be other valid values for the `precision` argument with an
implementation-defined meaning.

`(inexact-real-from-strings int frac precision exp) -> real`

Passed `int` and `frac` strings, the precision, and an `exp` string.

`int`, `frac`, and `exp` give the digits of the integer part, the
fractional part, and the exponent, respectively. It is an error to
pass strings containing characters other than decimal digits. Of
particular note is that no whitespace, underscores, commas, or periods
are permitted. A zero-length string has the same meaning as `"0"`.

However, the `int` and `exp` strings are allowed to start with a `+`
or `-` character indicating the sign of the integer part and the
exponent, respectively. It is an error if the sign is repeated, or if
it is not the first character of the string. A string consisting only
of a sign is not permitted; the sign is only valid when followed by
one or more digits. A string starting with a digit is interpreted the
same way as a string starting with a `+` sign.

`(inexact-real-from-integers int frac frac-length precision exp) -> real`

Passed `int`, `frac`, and `frac-length` integers, the precision, and an
`exp` integer.

`int` gives the integer part without the fractional part. `exp` is the
exponent (i.e. the power of 10).

`frac` are the digits making up the fractional part interpreted as an
integer in their own right. E.g. the `frac` value for `0.1234` would
be `1234`. The value for `0.0015` would be `15`.

`frac-length` says how many decimal places there are supposed to be in
the fractional part. The `frac-length` argument is needed to
distinguish fractional parts with different numbers of leading zeros.
E.g. the `frac-length` value for both `0.1234` and `0.0015` would be
`4`.

Pass an `int` or `exp` value of `0` for a number without an integer
part or an exponent, respectively. Pass `frac` and `frac-length`
values of `0` for a number without a fractional part.

All integers must be exact. One or both of `int` and `exp` may be
negative. It is an error to pass a negative `frac` or `frac-length`.

### Break reals

`(strings-from-inexact-real real frac-length) -> int; frac; exp`

`(integers-from-inexact-real real frac-length) -> int; frac; exp`

## Examples

```Scheme
(inexact-real-from-strings  "-123" "45"  'e "6")  ; => -123.45e6
(inexact-real-from-integers  -123   45 2 'e  6)   ; => -123.45e6
```

## Implementation

This sample implementation is slow and does not check the validity of
its input arguments. It uses `string-pad` from SRFI 13.

```Scheme
(define (inexact-real-from-strings int frac precision exp)
  (let ((precision (cond ((char?   precision) (string precision))
                         ((symbol? precision) (symbol->string precision))
                         ((string? precision) precision))))
    (string->number (string-append "#d" int "." frac precision exp))))

(define (inexact-real-from-integers int frac frac-length precision exp)
  (inexact-real-from-strings
   (number->string int)
   (string-pad (number->string frac) frac-length #\0)
   precision
   (number->string exp)))
```

## Acknowledgements

## References

Robert G. Burger and R. Kent Dybvig. [**Printing floating-point
numbers quickly and
accurately.**](https://legacy.cs.indiana.edu/~dyb/pubs/FP-Printing-PLDI96.pdf)
In Proceedings of the ACM SIGPLAN ’96 Conference on Programming
Language Design and Implementation, pages 108–116.

William Clinger. [**How to read floating point numbers
accurately.**](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.164.813&rep=rep1&type=pdf)
In Proceedings of the ACM SIGPLAN ’90 Conference on Programming
Language Design and Implementation, pages 92–101. Proceedings
published as SIGPLAN Notices 25(6), June 1990.

Sample implementation of the C language function
[`atof()`](http://www.beedub.com/Sprite093/src/machserver/1.098/libc/atof.c).
It converts a string to a double float.

Common Lisp [`decode-float`
function](http://clhs.lisp.se/Body/f_dec_fl.htm).

## Copyright

Copyright (C) Firstname Lastname (20XY).

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
