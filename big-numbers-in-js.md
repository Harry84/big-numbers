## Workarounds for using Big Numbers in JS


Cryptography is central to our well-being - as we increasingly place our personal information in the digital realm, we become ever more reliant upon it.  Applications of cryptography include ATM cards, computer passwords, and broadcast encryption.  Big Numbers are [integral](https://www.youtube.com/watch?v=M7kEpw1tn50) to cryptography and therefore the ability to maintain precision whilst handling them is vital to our safety and security.

<!--![Caesar Cipher](https://upload.wikimedia.org/wikipedia/commons/4/4a/Caesar_cipher_left_shift_of_3.svg)-->

<img style="margin:0px auto;display:block" src="https://upload.wikimedia.org/wikipedia/commons/4/4a/Caesar_cipher_left_shift_of_3.svg" width=70% align="middle"/>

As specified by the ECMAScript standard, all arithmetic operations in JavaScript are done using double-precision floating-point arithmetic.  The "floating-point" format allows for a trade off between precision and range: a number is approximated to a fixed amount of "significant digits" (called the _Significand_) which is then scaled by an exponent.  Given a certain total amount of digits to play with, the decimal point (or binary point in computing) can "float" relative to the Significand, increasing the range of real numbers that can be represented.  Essentially, "floating-point" is a formulaic representation of a real number.  It occupies 64 bits of computer memory.  

But this system has its limitations.  The binary floating-point format used by JS (binary64) has a standard specification:

* **Sign bit**: 1 bit

* **Exponent**: 11 bits

* **Significand precision**: 53 bits

Crucially, this gives us 15-17 significant decimal digits worth of precision.  But what if we want to perform calculations on number strings with a larger number of digits?  Our real integer values will no longer be precisely represented by the number format and will suffer from rounding errors.

This is essentially similar to overflow: an inherent limitation of fixed-precision arithmetic.  Consider a 4-digit Odometer's display which changes from 9999 to 0000.  Similarly, a fixed-precision integer sometimes exhibits wraparound if numbers involved grow too large to be represented at the fixed level of precision. Some processors overcome this problem via saturation (if a result can't be represented, it is replaced with the nearest representable value).

But what about circumstances where we need to avoid overflow and saturation?  For example, the 53 bit limit matters whenever an API returns 64 bit numbers.  The Twitter API encodes tweets in JSON as follows:

    > {"id": 10852385574690032345, "id_str": "10852385574690032345", ...}

The Twitter IDs detailed above are 64 bits long.  JSON is a text format (so it can represent integers of arbitrary size) but precision will be lost in JavaScript when the ID is parsed:

    > parseInt("10852385574690032345")
      10852385574690032000

Therefore to retain the precise value of an ID in JavaScript, it needs to be stored in a string or an array.

How can we perform basic operations on Big Numbers in JS?  Here is a quick guide to a possible approach to performing addition (which incidentally may help in the Kata linked below):

First of all, we need our two numbers stored in arrays, allowing us to maintain precision.  Next, we need to ensure we allow for place value when combining our two arrays.  We do this by reversing the arrays.  This way, a[0] and b[0] are the least significant digits in their respective arrays and the place value is the same for both.  Now we can loop through the arrays, adding each matching pair.  But what if an addition yields a sum greater than 10?  We then need to store 1 in a 'carry' variable which can then be added during the next iteration where the place value has increased by a factor of 10 (so our 1 is now worth 10).  Also, if there's a 'carry' left over at the end, we need to add that too.  This method follows the same principle as basic addition with pen and paper!

```JavaScript

var a = []; // Your 1st number
var b = []; // Your 2nd number
var c = []; // Store the result in c
var carry = 0;                   // Declare carry variable
for (var i = 0; i < a.length; i++) {

    c[i] = a[i] + b[i] + carry;

    if(c[i] >= 10) { // Sum too large to fit into this place value
        carry = 1;           // Set carry value to be added into the next digit
        c[i] -= 10;   
    }
    else {
        carry = 0;           // Sum was less than 10, so there's nothing to carry forward
    }
}
if (carry) { // Add the final carry if necessary
    c[i] = carry;
}

```

No doubt there are other approaches to adding Big Numbers together.  There is definitely scope for this article to be extended, both in terms of types of Operations and in terms of describing alternative methods for handling Big Numbers and their real world applications.

### References

[Floating Point Ref](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)

[Twitter Example Ref](http://www.2ality.com/2012/07/large-integers.html)

[Ref re Operations](https://silentmatt.com/blog/2011/10/how-bigintegers-work/)

[A relevant Kata](https://www.codewars.com/kata/sum-strings-as-numbers)
