# Floating Quotient Data Type

Recently I have been working on my *forever in the future* programming language March, and studying the various options for numerical data types. The usual selection of almost any programming language are the staple Integer and Floating Point (IEEE 754). Beyond that one will find a wide variety of more capable but much slower types offering *arbitrary precicion*, such as Rational and BigNum.

In my consideration of supporting floating-point, I like many others find the sacrific of accuracy unsettling. It's not as if the failing only occurs with nose-bleed large or small numbers. The inaccuracies creep in with everyday figures. 

    10.2 - 5.3 = 4.8999999999999995
    10.2 * 5.3 = 54.059999999999995

Not only are such simle computions inaccurate in general, but the exact results can vary from language to laguage. Of course, in many cases these inaccuracies are of no importance. When they are not, we programmers have learned techqniues to wrestle them into submition or completely bypass them if necessairy (hence the existence of arbitrary preciscion libraries).

But wouldn't it be nice if there were a data type that had similar perfomarce characteristcs to floating-point and, if not arbitrary preciscion, at least complete preciscion within the limits of it storage size? Is such a thing really not possible? Unfortunately is one respect, it is not possible. Modern CPUs have FPUs built-in to speed up floating-point calculations. So in that respect no software implementation of an alternate data type can compare in preformace. But putting things on a equal footing, I still ask, is there an alternative that could also find implementation in hardware eventually?

If you dig into internet search results looking for such a solution, the first potential candidate you are likely to come acorss is [Unum](https://en.wikipedia.org/wiki/Unum_(number_format)), which is a very grandious abbreviation for Universal Number. However, for all its fan far, Unum turns out to be little more than a more refined variant of float-point. Perhaps it's most innovative notion is the *ubit*, which allows the data type to specify its inpercision with percision ;) and secondly the variability of the significand and exponent size, which adds a modicum of additional precision. No doubt, in some ways it is an improvement, but in others it is simply taking matters beyond practial reason.

In my research I have to mention what is probably the most mind-bending and amazing numerical representations I came acrossed is called [Quote Notation](https://en.wikipedia.org/wiki/Quote_notation). ..

So here is the idea that occurs to me. Instead of storing a ... in a fixed number of bits, store a mixed fraction with a variable size for the whole ands quotient parts. It takes only a few bits to specify the size these parts and the rest encode the number ... For example, using only 16-bits, we can encode `1 1/2` as:

    | 0 || 0 | 1 | 0 || 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 || 0 | 1 || 1 | 0 |
    
Where the first bit is the sign bit (positive or negative), the next three bits indicate the size of the denomenator of the fraction. The numerator is always the same size as the numerator becuase it can never be greater in value than denomenator's value (otherwise it would need to be reduced). 

    |  word size |  q bits  |  min    |  max   |  mid  |
    |  32 bits   |   4      | 1/2^13  |  2^27  |  2^9  |
    |  64 bits   |   5      | 1/2^29  |  2^58  |  2^19 |
    |  128 bits  |   6      | 1/2^60  |  2^121 |  2^40 |
    |  256 bits  |   7      | 1/2^124 |  2^249 |  2^83 |

