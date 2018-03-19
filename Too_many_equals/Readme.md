The challenge consisted of several base64 lines that gave quotes from Edward Snowden(Take a look to the file).

According to the standard, base64 encoding works by mapping 6-bit groups of bits in the data to characters in a 65-character subset of US-ASCII.

#### The alphabet is: ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
So, the information to be encoded is split into 6-bit groups, and as the maximum 6-bit number (111111) is 63, the decimal value of each 6-bit pair can be used as an index into the alphabet array. To help wrap our heads around this, let's encode `hello` by hand using Base64:

We start by converting `hello` to binary, which yields:

`01101000 01100101 01101100 01101100 01101111`

Now we rearrange it into 6-bit pairs:

`(011010) (000110) (010101) (101100) (011011) (000110) (1111 ??)`

Looks like we've run into an issue. Accoring to the standard, when our bits aren't evenly divisible by 6, we have to add padding characters to the end of our base64 encoded string. Because we have 2 bits leftover, the padding looks like this:

`(011010) (000110) (010101) (101100) (011011) (000110) (1111=00)`

We fill the extra space with zeros, which are then flagged by the `=` character for the decoding algorithm. That tells it to ignore the last 2 bits of the encoded string. With `==`, it ignores the last 4 bits. This is what `too_many_equals` means. Let's translate this grouping to base64:
```
    6 Bit Groups: (011010) (000110) (010101) (101100) (011011) (000110) (1111=00)
   Decimal Index:    26       6        21       44       27       6        60
Base64 Character:    a        G        V        s        b        G        8=
```
This yields a base64 string of `aGVsbG8=`. However, in performing this exercise it has become apparent that base64 decoding algorithms will ignore 2 bits in the `8=` portion of this string. To test this, let's add 000010 (2) to `8=`, yielding `+=`. If we add that to the encoded string, we get `aGVsbG+=`. If we decode this using a base64 decoder, it still decodes to `hello` 

It looks like we can hide some bits in the padding of base64 encoded text. If the encoded text ends in `=`, the decoder ignores 2 bits, and if 4 bits if it ends in `==`. If we take each line that ends in `=` or `==`, we can grab the appropriate bits and try to put them together into a string. my decrypt.py will do this.

