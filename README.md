
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
Various binary search methods in java.util.Arrays compute the average of two positive indices (ints) like this:

	    int mid = (low + high) >> 1;

but if low + high overflows into negative territory, the result is negative,

probably giving undefined results.  An easy fix is the simple

	    int mid = (low + high) >>> 1;
        
## HashMap 

### [HashMap race condition](http://mailinator.blogspot.fr/2009/06/beautiful-race-condition.html)
    
## ConcurrentHashMap 
* More often, ConcurrentHashMap is introduced to improve performance: internally ConcurrentHashMap allows concurrent threads to read values 
without locking at all, except in a minority of cases, and for concurrent writers to add and update values while only acquiring locks over 
localised segments of the internal data structure. Read operations are therefore mostly synchronization free and have greatly improved performance, 
and if there are many concurrent writers then lock contention will be reduced improving performance even further.
 
* the fully parametized constructor of ConcurrentHashMap has 3 parameters, initialCapacity, loadFactor and concurrencyLevel,
  ConcurrentHashMap shards its data into segments to reduce writer lock contention. the concurrencyLevel parameter directly 
  specifies the number of shards that are created by the class internally. The idea is that the number of shards should equal 
  the number of concurrent writer threads that you normally see. And, the default value for concurrencyLevel is 16!
  
  Once you start using ConcurrentHashMap as a matter of course, the number of instances allocated can grow dramatically. 
  Consider for instance that you are building a game server that maintains contextual data for each connected user. You might associate a ConcurrentHashMap
  with each connected user allowing multiple threads to query and update online user’s contextual information with a high degree of performance and safety
  In principle this works fine, but in practice you may find your game server has several thousand users connected, which drives the allocation of several 
  thousand ConcurrentHashMap instances. Now, when you do a heap dump of your server and have a poke around, you notice there are millions and zillions of 
  ConcurrentHashMap$Segment, ConcurrentHashMap$HashEntry[] and ReentrantLock$NonfairSync objects. This is because, in fact, the creation of a single ConcurrentHashMap 
  instance for each of say 5000 users minimally results in 240,000 of these objects being allocated even before any values are added to the maps.

* So Best Practice usage
```java
ConcurrentHashMap<String, MyClass> m = new ConcurrentHashMap<String, MyClass>(8, 0.9f, 1);
```
 In the above, only a single shard segment is created internally that allocates an initial capacity for its HashEntry[] table of 8, which allows for 
 some reasonable number of values to be added before reallocation, and the load factor of 0.9 ensures reasonably dense packing. The single shard offers 
 full read benefits and, unless you have very high concurrency sufficient write throughput without risking crazy unnecessary memory loading.

 For completeness it is worth pointing out that there are indeed places where you might want to increase segment numbers, but such applications are 
 fairly unusual and specialised.

 



