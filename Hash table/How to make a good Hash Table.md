# Hash table

## What is a hash table

A hash table is a key-value data structure that implements an associative array or dictionary. It store data buckets as a pair of key-value. 

### Key

The key is a value assigned by the data structure to the value, usually generated through the use of a hash function.

### Value

I will use the word "node" and "bucket" interchangably to refer to the value in the key-value pair

### Why hash table

Hash tables are **incredibly** popular in software development. Most of the time, if your data is not stored in a schema-based table, good chance are it will be stored in a hash table in the form of key-value pairs. If you have a schema-based database, there is likely a hash table somewhere to serve as a cache infrastructure for your database.

## Implemenetation of a hash table

### What should you keep in mind when selecting/designing a hash table

There are three things to pay attention to:
---

#### What is your hash function

Most of the performance of your hash table would be affected by the performance on your hash function. There are hash functions specialized for a subset of data, others are general purposes. Regardless, careful consideration should be paid to select a good hash function.

#### How do you structure you hash node and hash table

Where you hash tables and nodes live:

If you are "hot-loaded" (ie. all inside L1/L2/L3 cache), things are infintely faster than "cold-stored" (RAM). However, stack size are limited from a few kB to single-digits mB, so a hash table with big-sized nodes might need to be stored in the heap.

What algorithm do you use to implement the "find" feature:

This is crucial since the whole intention of a hash table in first place is to find the data stored in the fastest way possible. Typically, a search-type algorithm is used.

Bar exotic search algorithms, the two most popular way to implement this feature are **Linear search** and **Binary Search**.

In my experience, if your hash table generally have <100 nodes, Linear search is the way to go. Above that, Binary search take the cake, especially at >10,000 nodes; since time complexity of Binary search increase in logN instead of Linear's N. 

Bear in mind that both are affected by your comparison algorithm, so how you store the key is also relevent.

#### How do you handle table-wise functions.

There are some details you must consider regarding the hash table as a whole: Collision, Load factor, Rehashing and Resizing

**1. What is Collision?**

**Collision** is where a hash sequence is the same for more than one hash node; when retrieving data, this is not acceptable to pull two buckets from a single hash. 
There are a few ways to handle collisions, but primarily they can be grouped into:


* ***Separate chaining***: This worked using more than one hash functions to compute several hashes for one value. One hash is chosen as the bucket's top-level hash, and each following value(s) have a hash when there are several values for on top-level hash. 

The values can then be grouped together using a list or a tree.

* ***Open Addressing***: When a collision happen, the hash function automatically searches for another empty space on the hash, then assigns the hash function of the newer collided node to the new empty space; this process is called "probing". Probing distance can be linear (linear probe), exponetial (quadratic probe), or based on calculation from another hash (double hashing).

I see people often opted for open addressing when designing large-scale hash table, so I recommeded this approach.

**2. What is Load factor?**

Load factor is how many spots filled versus how many inital spots in your hash table. As Load factor increasing to 1, the chance of a collision grow, which when triggered hampers performance.

Also, when hash table is over-utilized (load factor grow close to 1), we need to increase the size of the hash table. Conversively, we should also decrease table size when it is under-utilized (load factor close to 0).

I find that the thresholds for load factors are best at 0.1 - 0.7. Bigger table can tolerate more extreme thresholds, at the expense of "insert" and "find".

**3. What is Rehashing?**

When a table need to resize, it need to move the nodes to a "new" hash table. Thus it need to calculate all the hashes of the nodes currently in the "old" hash table. This process is calle rehashing.

As it sound, rehashing is an incredibly expensive operation; people go to great length to avoid having to rehash their table, and MTBR (mean-time-between-rehash) is a metric to measure how good your hash table algorithm, the bigger the better.

#### What is your data?

Envision what is your dataset characteristic can go a long way into optimizing your hash table performance.

**1. Take a look at your peers:** Looking at how people solving your problems can save you a lot of time, especially for primitives datatypes. 

**2. Be mindful of your enviroment:** Keep in mind the operating enviroment of your data structure and optimize for it; one example is sometimes more instructions do not equals slower operations. On the other hand, not every tricks of the trades are worth implementing; modern compiler can optimize for conditional statements pretty good so branchless programming should be consider carefully as is it worth more complex code.

**3. Game your dataset:** No matter how good your way theoratically, real tests are the gold standard. Gdb and iperf are your friends, consult them often.