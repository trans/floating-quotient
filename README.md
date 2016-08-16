# Floating Quotient Data Type

Recently I have been working on my *forever in the future* programming language [March](), and studying the various options for numerical data types. The usual choices of almost every programming language are the staple Integer and Floating Point (IEEE 754) number formats. Beyond that one will find a variety of more capable *arbitrary precicion* formats, such as Rational and BigNum, but with the trade-off of much slower performance.

In my consideration of supporting floating-point, I like many others find the sacrific of accuracy unsettling. It's not as if the failing only occurs with nosebleed large or small numbers. The inaccuracies creep in with everyday figures. 

    10.2 - 5.3 = 4.8999999999999995
    10.2 * 5.3 = 54.059999999999995

Not only are such simple computions inaccurate, the exact results can vary from language to laguage. Of course, in many cases these inaccuracies are of no importance. And when they are, we programmers have learned techqniues to wrestle them into submition, or completely bypass them if necessairy (hence the existence of arbitrary preciscion formats). And yet...

Wouldn't it be nice if there were a data type that had similar perfomarce characteristcs to floating-point and, if not arbitrary preciscion, at least complete preciscion within the limits of it storage size? Is such a thing really not possible? Well,  in one respect it is not. Modern CPUs have FPUs built-in to speed up floating-point calculations. So in that regard, no software implementation of an alternate type can possibly compare in preformace. But putting things on a equal footing, I think it still fair to ask, is there an comprable alternative -- one that could might also find implementation in hardware eventually?

If you dig into internet search results looking for such a solution, the first potential candidate you are likely to find is [Unum](https://en.wikipedia.org/wiki/Unum_(number_format)), which is a very grandious abbreviation for "Universal Number". However, for all its fanfare, Unum turns out to be little more than a more refined variant of float-point. Perhaps it's most innovative notion is the *ubit*, which allows the data type to specify its inpercision with percision ;) and secondly the variability of the significand and exponent size, which adds a modicum of additional precision. No doubt, in some ways it is an improvement, but in others it is simply taking matters beyond practial reason.

In the course of my research, I must also mention what is probably the most mind-bending and amazing number format I came across. It is called [Quote Notation](https://en.wikipedia.org/wiki/Quote_notation). ... Unfortunately it has one glaring problem. To work, the computer is required to recognize reapeating patterns, and that, unfortunately, does not seem a simple enough prodecure.

And that was about it. I know there must be more possibilities, probably gathering digital dust in journals of Mathematics and Computer Science. But insofar as a Google search carries, that is pretty much the extent of concrete solutions I found.

So here then is an idea that has occured to me. Instead of storing a significand and exponent in a fixed number of bits, we instead store a mixed fraction with a variable size for the whole ands quotient parts. It takes only a few bits to specify the size of these parts and the rest are available to encode the number.

For example, using only 16-bits, we can encode `1 1/2` as:

    | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 1 | 0 |
     ___ ___________ _______________________________ _______ _______
     sign  q-size                whole                 num.   denom.
    
Where the first bit is the sign bit (positive or negative), the next three bits indicate the size of the numerator and  denomenator of the fraction, which are always the same size since the numerator's value can never be greater in value than denomenator's (otherwise it would need to be reduced). 

In this way we can represent a refined swath of rational numbers, far more than floating-point, without loss of preciscion within the limits of the unit size, and with uniformity. 

Compactness is also fairly good. Although not nearly the breadth of float-point, the trade-off for precision and unifority appear more than adequate. Consider the following table.

    |  Size      |  Q Size  |  Min    |  Max   |  Mid  |
    |  32 bits   |   4      | 1/2^13  |  2^27  |  2^9  |
    |  64 bits   |   5      | 1/2^29  |  2^58  |  2^19 |
    |  128 bits  |   6      | 1/2^60  |  2^121 |  2^40 |
    |  256 bits  |   7      | 1/2^124 |  2^249 |  2^83 |

The Q-size is the number of bits required to store the size of the denomenator (and numerator). From this we can calculate the size of the whole `W = T - (2Q + 1)`, where T is the total bit size and the `1` is subtracted for the sign bit. The *Mid* is the middle value where the number of bits dedicated to the significand is the same as those decidcated to the numerator and denomentator. Considering the limits of each word size, 32-bits is restrained, having a maximum mid-range value of 512. (In other words, if we needed fractional accuracy of 1 in 512, the whole portion could not exceed 512.) But 64-bits appears adaqute for most practical applications, and certainly 256-bits is more than adquate for all but the most esoteric applications.

As for speed, I did a limited comparison of Ruby's floating point versus it's rational representation and found a difference of about 5x. While a very rough guestimation, I am confident a hardware implmentation of floating-qoutients would allow nearly equatable speeds.

There are some tweaks that might be made to this format. For instance, the denomenator can't ever be just `1`, as that would indicate a whole number, not a fraction. And if it were `0`, it could only indicate an infinity. So the denomenator could alwasy be the value plus two. Likewise, because the numerator can never be less than the denomenator, the numerator could be the value plus three. Another option is for the numerator and denomenator to always be at least one bit in size, or for it to always come in sets of 2 bits. The former would reduce the number of redundent representations a little, and the later trade a bit from the significant to grant us an extra bit on the fractional part. However, the gains garnered from any of thes tweaks  may not outweight the added complexity to implmentation. That remains to be ascertained.

