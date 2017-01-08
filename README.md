# pd
Printable Data: a data de/serialization

## Why another data de/serialization?

Because its not easy to find a good one that is:
- Standardized
- Human readable/printable
- Fast encoding/decoding
- Light weighted
- Easy toolchain

On the contrary, data size and streaming capability are traded off.

Finally,
[S-Expression by Ron Rivest](http://people.csail.mit.edu/rivest/Sexp.txt)
is choosed as the starting point of pd.

## Specification

### S-Expression

A token is an octet-string. A list is an ordered collection noted by a pair
of bracket `'('` and `')'`. An S-Expression is a list of token or list.

### Representation

S-Expression has various ways of representation. Pd limits it into three ways:
- Canonical representation:
  Each pd has a unique canonical representation in binary form.
  It can be used as data representation for digital signature.
  It is easy and fast in marshalling / unmarshalling.
- Transport representation:
  Transport is a base64 encoded Canonical representation in pair of `'{'` `'}'`
  for text mode or canonical representation itself in binary mode.
- Printable representation:
  The printable representation is pretty-printted human readable in text mode.
  It is encoded in utf-8 by default.

### Canonical Representation

Token in Canonical Representation is a byte length followed by a column and the
data itself:

    (4:text)
    (1:06:xxxxxx(3:abc2:ok))

The byte length and column is in the ASCII encoding (binary 0x30-0x3A).
The list notion `'('` and `')'` is in the ASCII encoding (binary 0x28, 0x29).
the data is 'verbatim' without encoding.

### Transport Representation

Binary mode Transport Representation is the same as Canonical Representation.

Text mode Transport Representation is the
[base64](https://tools.ietf.org/html/rfc4648) encoded Canonical Representation
without outer bracket and wrapped in `'{'` `'}'` (binary 0x7B, 0x7D) pairs.

    {NDp0ZXh0Cg==}
    {MTowNjp4eHh4eHgoMzphYmMyOm9rKQo=}

### Printable Representation

Printable representation is a human readble version of canonical representation.

It omits the prefixed byte length and the column.
It prints out nested list in a indented way.
It prints out the item in list as individual rows.
It prints out the binary octet-string as is if all of them falls into the 7-bit
printable ascii (0x20-0x7E) and '\t' 0x09 '\n' 0x0A '\r' 0x0D. Or the one byte
spaced hex encoding.

    (test)
    (0
     XX XX XX XX XX XX
     (abc
      ok))
