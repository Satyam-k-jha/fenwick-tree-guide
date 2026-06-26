Fenwick Tree (Binary Indexed Tree)

«A complete beginner-friendly guide to understanding, implementing, and mastering the Fenwick Tree (Binary Indexed Tree).»

---

Table of Contents

- Introduction
- What Problem Does a Fenwick Tree Solve?
- Brute Force Solution
- Prefix Sum
- Why Prefix Sum Fails
- What is a Fenwick Tree?
- Binary Numbers
- Bitwise Operators
- Two's Complement
- Understanding "i & (-i)"
- Fenwick Tree Structure
- Update Operation
- Query Operation
- Range Sum Query
- Time & Space Complexity
- Complete C++ Implementation
- Common Mistakes
- Applications
- Practice Problems
- References

---

Introduction

A Fenwick Tree, also known as a Binary Indexed Tree (BIT), is a data structure that efficiently supports two operations on an array:

1. Updating the value of an element.
2. Finding the sum of a prefix or a range.

If we use a normal array, updating an element is fast but finding the sum of a large range is slow. If we use a Prefix Sum array, finding a range sum is fast but updating an element becomes slow because many prefix values must be recomputed.

The Fenwick Tree provides a balance by supporting both update and query operations in O(log n) while using only O(n) extra memory.

It is widely used in:

- Competitive Programming
- Coding Interviews
- Online Query Processing
- Frequency Counting
- Inversion Counting
- Coordinate Compression
- Order Statistics
- Dynamic Prefix Sum Problems

---

The Problem

Consider the following array:

Index : 1 2 3 4 5 6 7 8
Value : 5 3 7 2 6 1 4 8

Suppose we receive these queries:

Sum(1,4)

Sum(3,8)

Sum(2,6)

Sum(5,8)

The simplest approach is to add every element inside the range.

Example:

Sum(2,6)

=

3+7+2+6+1

=

19

This works, but if the array contains 100,000 elements and we receive 100,000 queries, scanning the array repeatedly becomes extremely slow.

---

Brute Force Solution

Pseudo code:

int sum = 0;

for(int i = left; i <= right; i++)
    sum += arr[i];

Complexity

Operation| Complexity
Query| O(n)
Update| O(1)

This approach is inefficient when the number of queries is very large.

---

Prefix Sum

A Prefix Sum array stores cumulative sums.

Original array:

5 3 7 2 6

Prefix array:

5
8
15
17
23

Now,

Sum(2,5)

=

Prefix[5]-Prefix[1]

=

23-5

=

18

Amazing!

The query becomes O(1).

Complexity

Operation| Complexity
Build| O(n)
Query| O(1)
Update| O(n)

---

Why Prefix Sum Fails

Suppose

Array

5 3 7 2 6

Now update

arr[3]=100

The array becomes

5 3 100 2 6

Now every prefix value after index 3 changes.

Old Prefix:

5
8
15
17
23

New Prefix:

5
8
108
110
116

Updating a single element requires recalculating almost the entire Prefix Sum array.

This is why Prefix Sum is unsuitable when updates are frequent.

---

What is a Fenwick Tree?

A Fenwick Tree stores partial sums instead of storing the original array values.

Each index stores the sum of a carefully selected range.

The size of that range depends on

index & (-index)

called the Lowest Set Bit (LowBit).

Because each operation jumps between ranges instead of visiting every element, both update and query operations require only O(log n) time.

---

Binary Numbers

Computers store everything in binary.

Decimal numbers use ten digits.

0 1 2 3 4 5 6 7 8 9

Binary numbers use only two digits.

0 1

Examples:

Decimal| Binary
1| 0001
2| 0010
3| 0011
4| 0100
5| 0101
6| 0110
7| 0111
8| 1000
9| 1001
10| 1010
12| 1100

Example:

1101

=

8+4+1

=

13

---

Bitwise AND

Truth Table

A| B| A&B
0| 0| 0
0| 1| 0
1| 0| 0
1| 1| 1

Example

13

1101

10

1010

------

1000

=

8

---

Two's Complement

Negative numbers are represented using Two's Complement.

Steps:

1. Invert all bits.
2. Add one.

Example

12

00001100

Invert

11110011

Add one

11110100

This represents

-12

---

Understanding "i & (-i)"

This is the heart of the Fenwick Tree.

Example

12

1100

-12

0100 (relevant bits)

AND

1100

0100

=

0100

=

4

Example

10

1010

1010

0110

=

0010

=

2

Therefore,

lowBit = i & (-i);

returns the value of the lowest set bit.

Examples

i| Binary| LowBit
1| 0001| 1
2| 0010| 2
3| 0011| 1
4| 0100| 4
5| 0101| 1
6| 0110| 2
7| 0111| 1
8| 1000| 8

---

Fenwick Tree Structure

Suppose

Array

5 3 7 2 6 1 4 8

Each tree node stores a range.

Tree Index| Stores
1| A[1]
2| A[1]+A[2]
3| A[3]
4| A[1]+A[2]+A[3]+A[4]
5| A[5]
6| A[5]+A[6]
7| A[7]
8| A[1]...A[8]

General Rule:

«Tree[i] stores the sum of the last "lowBit(i)" elements ending at index "i".»

---

Update Operation

Suppose

arr[5]+=3

Every Fenwick node responsible for index 5 must be updated.

Algorithm

while(index<=n)
{
    tree[index]+=value;
    index+=index&(-index);
}

Example

Update index =5

↓

Tree[5]

↓

Tree[6]

↓

Tree[8]

↓

Stop

Only three nodes are updated.

Complexity

O(log n)

---

Prefix Query

To find

PrefixSum(7)

Algorithm

while(index>0)
{
    answer+=tree[index];
    index-=index&(-index);
}

Traversal

7

↓

6

↓

4

↓

0

Answer

Tree[7]

+

Tree[6]

+

Tree[4]

Complexity

O(log n)

---

Range Sum Query

Formula

Sum(L,R)

=

Prefix(R)

-

Prefix(L-1)

Example

Sum(3,6)

=

Prefix(6)

-

Prefix(2)

---

Complexity Analysis

Operation| Complexity
Build| O(n log n)*
Optimized Build| O(n)
Update| O(log n)
Query| O(log n)
Range Query| O(log n)
Space| O(n)

---

Complete C++ Implementation

#include <iostream>
#include <vector>
using namespace std;

class FenwickTree
{
private:
    vector<int> tree;
    int n;

public:
    FenwickTree(int size)
    {
        n = size;
        tree.assign(n + 1, 0);
    }

    void update(int index, int value)
    {
        while(index <= n)
        {
            tree[index] += value;
            index += index & (-index);
        }
    }

    int query(int index)
    {
        int sum = 0;

        while(index > 0)
        {
            sum += tree[index];
            index -= index & (-index);
        }

        return sum;
    }

    int rangeQuery(int left, int right)
    {
        return query(right) - query(left - 1);
    }
};

int main()
{
    vector<int> arr = {0,5,3,7,2,6,1,4,8};

    FenwickTree bit(8);

    for(int i = 1; i <= 8; i++)
        bit.update(i, arr[i]);

    cout << bit.rangeQuery(2,6) << endl;

    bit.update(5,3);

    cout << bit.rangeQuery(2,6) << endl;

    return 0;
}

---

Dry Run

Array

5 3 7 2 6

Insert 5

Tree[1]

Tree[2]

Tree[4]

Insert 3

Tree[2]

Tree[4]

Insert 7

Tree[3]

Tree[4]

Insert 2

Tree[4]

Continue similarly until all elements are inserted.

---

Common Mistakes

1. Forgetting 1-Based Indexing

Fenwick Trees are usually implemented using indices starting from 1.

---

2. Infinite Loop

Incorrect

while(index<n)

Correct

while(index<=n)

---

3. Using Zero Index

The expression

0 & (-0)

returns zero.

The loop never progresses.

Always start indexing from 1.

---

4. Wrong Range Query

Incorrect

query(right)-query(left)

Correct

query(right)-query(left-1)

---

Applications

Fenwick Trees are commonly used in:

- Dynamic Prefix Sum
- Frequency Tables
- Order Statistics
- Inversion Counting
- Coordinate Compression
- Offline Queries
- Dynamic Rankings
- Histogram Processing
- Competitive Programming
- Database Aggregation
- Financial Analytics

---

Fenwick Tree vs Prefix Sum

Feature| Prefix Sum| Fenwick Tree
Update| O(n)| O(log n)
Query| O(1)| O(log n)
Memory| O(n)| O(n)
Dynamic Updates| No| Yes

---

Fenwick Tree vs Segment Tree

Feature| Fenwick| Segment
Implementation| Easy| Hard
Memory| O(n)| O(4n)
Update| O(log n)| O(log n)
Query| O(log n)| O(log n)
Range Updates| Limited| Excellent

---

Practice Problems

Easy

- Prefix Sum Queries
- Dynamic Frequency Array
- Point Update Range Sum

Medium

- Inversion Count
- Count Smaller Numbers
- Offline Range Queries

Hard

- 2D Fenwick Tree
- Range Update Range Query
- Dynamic Order Statistics

---

Key Takeaways

- Fenwick Tree is also called Binary Indexed Tree (BIT).
- It supports point updates and prefix sum queries in O(log n).
- It uses only O(n) memory.
- The expression "i & (-i)" extracts the lowest set bit.
- Every tree node stores the sum of a specific range ending at its index.
- Range queries are computed using:
  Prefix(R) - Prefix(L - 1)
- Fenwick Trees are simpler than Segment Trees while providing similar performance for many range-sum problems.

---

References

- Peter Fenwick, A New Data Structure for Cumulative Frequency Tables (1994)
- Competitive Programming Handbooks
- CP Algorithms
- The Algorithm Design Manual

---

⭐ If this guide helped you understand Fenwick Trees, consider giving this repository a star and sharing it with others. Happy Coding!
