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

`(inexact-real-from-strings int frac exp) -> real`

Passed three strings with the base-10 digits of the integer part,
fractional part, and the exponent, respectively. Returns a real number
built from those using the same method as the Scheme implementation's
version of `string->number` does.

All strings must contain only digits with no whitespace or other
characters. The `int` and `exp` strings are allowed to start with a
`+` or `-` character indicating the sign of the integer part and the
exponent, respectively. No sign is the same as a `+` sign.

`(inexact-real-from-integers int frac frac-length exp) -> real`

Passed four integers. Returns a real number built from those using the
same method as the Scheme implementation's version of `string->number`
does.

* `int` is the integer part and can be negative.

* `frac` are the fractional digits taken as a whole number and must be
nonnegative. E.g. the `frac` value for `0.1234` would be `1234`. The
`frac` value for `0.0015` would be `15`.

* `frac-length` says how many decimal digits there are intended to be
in the fractional part. This information is needed to account for the
case where the fractional part has leading zeros.

* `exp` is the exponent (i.e. the power of 10) and can be negative.

### Break reals

`(strings-from-inexact-real real frac-length) -> int; frac; exp`

`(integers-from-inexact-real real frac-length) -> int; frac; exp`

## Examples

```Scheme

(inexact-real-from-strings "-123" "45" "6")  ; => -123.45e6
(inexact-real-from-integers -123   45 2 6)   ; => -123.45e6
```

## Implementation

This sample implementation is slow and does not check the validity of
its input arguments. It uses `string-pad` from SRFI 13.

```Scheme
(define (inexact-real-from-strings int frac exp)
  (string->number (string-append "#d" int "." frac "e" exp)))

(define (inexact-real-from-integers int frac frac-length exp)
  (strings->inexact/
   (number->string int)
   (string-pad (number->string frac) frac-length #\0)
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
