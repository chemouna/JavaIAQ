
# JavaIAQ

Inspired by Peter Norvig blog post [](http://norvig.com/java-iaq.html) and Java 
Puzzlers book, this is an attempt to expand more of it and put the subtle parts of java that i encounter here.

## Can overridden methods differ in return type ?
- An overridden method may have a more specific return type. That is, as long as the new return type is 
  assignable to the return type of the method you are overriding, it's allowed.
  also a method signature in java is the method name and the number and type of its parameters. 
  Return types and thrown exceptions are not considered to be a part of the method signature. 
  and the name of the parameter is not part of the method signature and is ignored by the compiler for checking method uniqueness.

 ## Integer Overflow 
 Various binary search methods in java.util.Arrays compute the average of
two positive indices (ints) like this:

	    int mid = (low + high) >> 1;

but if low + high overflows into negative territory, the result is negative,
probably giving undefined results.  An easy fix is the simple

	    int mid = (low + high) >>> 1;
        
