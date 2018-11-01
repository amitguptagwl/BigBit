# BigBit
An alternate and improved data type of decimal and floating point

Table Of Contents
* Abstract
* Introduction

# Abstract
Floating point arithmetic is a key component for the computer applications doing mathematical calculations specially the applications related to finance or science experiments. It requires large dynamic range and high level of accuracy. Here I'm proposing a new floating number format **BigBit** to store large range of decimal numbers in less space without losing any precision.

# Introduction

There are different representations used to store a number in computer memory as per either their length (number of digits) or size. Eg int32, int64, binary32 (IEEE 754 Single-precision floating-point format ), binary64 ( IEEE 754 Double-precision floating-point format ) etc. But these representations are limited to represent a number to certain size and precision. Eg int32 can represent an integer number up to 2,147,483.647 with 4 bytes fixed memory to store it. Whereas binary32 can represent a decimal number up to  3.402823 × 10^38^ (approx) with 4 bytes in memory. However you lose the precision after certain limit in decimal representation.

As there are different number formats can be used as per their possible values, a developer needs to have low level detail to carefully select a number type for a variable to store it. Otherwise unpredictable incorrect result may occur.

## Number formats

### Fixed point number
A fixed point number has a specific number of bits (or digits) reserved for the fractional part (the part to the right of the decimal point) and sometimes  a specific number of bits reserved for the integer part (the part to the left of the decimal point). No matter how large or small a number is, it will always use the same number of bits for each portion. For example, if a fixed point format was in decimal IIIII.FFFFF then the largest number can be represented would be 99999.99999 and the smallest would be 00000.00001 (considering positive numbers only).

Every bit of code that processes such numbers has to have built-in knowledge of where the decimal point is and a mechanism for rounding the numbers when they've more digits than expected. Eg a number 1.2999 can be rounded to 1.30 or 1.29 for I.FF representation.

### Floating point number
A floating point number does not reserve a specific number of bits for the integer part or the fractional part. With the understanding with *IEEE 754 floating point format* we can further say that it reserves a certain number of bits for the number (called the mantissa or significand) and a certain number of bits to say where within that number the decimal place sits (called the exponent).

## IEEE 754 standard

To understand BigBit format better, we need to understand its need  by comparing it with the widely used number format standard [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754).

IEEE Standard for Floating-Point Arithmetic (IEEE 754) is a technical standard for floating-point computation established in 1985. Many hardware, software, and other standards are using IEEE 754 standard. This standard specifies 2 formats of floating point numbers;
* Single-precision floating-point format
* Double-precision floating-point format

More formats were added later.

### [Single-precision floating-point format](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)
This format is also known as float or binary32. This format occupies 32 bits ( or 8 bytes) in computer memory. It can be  represented as follow;

![](https://lh6.googleusercontent.com/USnlCXRbJQtOOshF2YQgyKFTbFECr2TrSKggJHJ3FM-qTQgsGR0yg9BtyXFsk3iBLy_2luvXPST1sMuoPeC9e3n_76Repj3s_OOp2q0PK7TJhGh8qtl28ixb7uEP8d7F7zqPzAXD)

* Sign bit: 1 bit
* Exponent : 8 bits 
* Mantissa (fraction) : 23 bits 

This format can accurately represents number between 2<sup>-24</sup> and  2<sup>+24</sup> and all the numbers represented by 2<sup>n</sup>, where the value of n is in between -126 and +127.

However a number out of the given range above can also be stored on the cost of losing the precision. Eg. If we store a number 2<sup>24</sup> + 1, it actually stores as 2<sup>24</sup>.

### [Double-precision floating-point format](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)
This format is also known as double or binary64. This format is same as single precision format but it occupies 64 bits ( or 16 bytes) in computer memory. 

* Sign bit: 1 bit
* Exponent : 11 bits 
* Mantissa (fraction) : 53 bits 

Hence the higher range can be represented accurately i.e. all the numbers between 2<sup>-53</sup> and  2<sup>+53</sup> and all the numbers represented by 2<sup>n</sup>, where the value of n is in between -1022 and +1023.

### Decimal floating-point format
decimal32, decimal64, and decimal128 formats were added to IEEE 754 standard in year 2008. This format occupies 32, 64, and 128 bits in memory respectively. These formats were added to remove the rounding errors so that the numbers can be used by financial and tax computations. So, for instance, if we subtract 0.2 from 0.3, it is `0.3 - 0.2 = 0.09999999999999998` in case of precision floating point format and it is `0.3 - 0.2 = 0.01` in decimal floating point format.

Significand and exponent in this format can be encoded in multiple ways as there is not any specific way defined in the standard itself, eg. [DPD (Densely Packed Decimal)](https://en.wikipedia.org/wiki/Densely_packed_decimal), and [BID (Binary Integer Decimal)](https://en.wikipedia.org/wiki/Binary_integer_decimal).

# BigBit format

BigBit (aka ByteBit) format is pretty much similar to any ordinary binary number representation. However the maximum value of coefficient is 255 instead of 1 and base is 256 instead of 2.

### Binary format
Binary number can be represented by 0 or 1. A decimal number in binary format can be represented as

<div align="center">
  <img src="bin_formula.png"  width="100x">
  <img src="bin_equation.png"  >
</div>

We can use above expression to represent 23 in binary format as follows;

1 x 2<sup>4</sup> + 0 x 2<sup>3</sup> + 1 x 2<sup>2</sup> + 1 x 2<sup>1</sup> + 1 x 2<sup>0</sup>

And the series of coefficient can be represented as `10111` in binary, where each digit represents the value of 1 bit. Maximum value that can be represent is `11111` i.e. 31. But as it takes at least 1 byte (8 bits) in memory to represent this number, the maximum number a byte can represent is 255.

### BigBit format

Byte bit format consider 1 byte as bit and use the same equation to represent a decimal number. Since a byte can represent all the numbers between 0 and 255, we can re-write the equation as follows;

![](bigbit_equation.png)

We can use above expression to represent 4295033089 in ByteBit format as follows;

1 x 256<sup>4</sup> + 0 x 256<sup>3</sup> + 1 x 256<sup>2</sup> + 1 x 256<sup>1</sup> + 1 x 256<sup>0</sup>

And the series of coefficient can be represented as `[1,0,1,1,1]`, where each digit represents the value of 1 byte. Maximum value that can be represent with 5 bytes is `[255,255,255,255,255]` i.e. 1.099511628×10¹². 

Though it looks very similar to representation of binary number, the actual representation is reversed with an extra byte for head byte `[5,1,1,1,0,1]`. This representation makes it easy to implement and understand.
