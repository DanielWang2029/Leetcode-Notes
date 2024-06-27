
# Question specific thoughts

## 1. Binary Search

### 1482 Min days to make n bouquets

***Difficult: 7/10***
***Interesting: 6/10***
***Educating: 9/10***

![1482](images/1482.png)

The common way to solve this problem is binary search. Think about the runtime to check if a given result would satisfy the criteria. It suppose to be O(n). Then we can perform a binary search on the result within the max and min of the given days and would produce a result within O(nlog(n)). If the values of the days array is unbounded, we could sort the array first and perform binary search on the indexes of the array instead.

There's another approach to this problem: disjoint set. We could sort the days array and add each day sequentially while maintain a dictionary of the current status, i.e. ranges of blooming flowers. For each newly added position, we try to merge it into adjacent existing ranges, or create a new range itself, as well as maintaining the current max bouquets number, all in constant time. This way there's constant runtime for each position added and every position is added only once therefore the runtime for adding position is O(n). However, since we sorted the days array in the begining, the overall runtime is also O(nlog(n)).

## 2. Math

### 330 Patching array

***Difficult: 8/10***
***Interesting: 10/10***
***Educating: 1/10***

![330](images/330.png)

Lets first define a simple case where the array is "non-sparse", meaning all numbers within the range of the values, min to max, could be written into a sum. For example, [1, 2, 3, 5, 9] would be a "non-sparse" array where [1, 2, 4, 9, 15] is not because 8 cannot be written into a sum.

From here, It's reasonable to guess that:

1) For a given "non-sparse" array, the next value to be patched/added (if needed) is the sum of the array plus 1. In other words, given a "non-sparse" array arr, the smallest number that cannot be written as the sum of current arr values is the sum(arr) + 1

2) Adding the smallest missing/unreachable/unsumable number is better than adding any number that is smaller than it.

Given these guesses, it's easy to derive the final solution. The hard part, however, lies within the proof of these assumptions.

For guess #1, we can prove it by induction:

Consider the smallest number that cannot be written as a sum of current values, which lets call s.

First we want to argue that s should be larger than the largest value inside arr, which lets call x1. This is because by definition of "non-sparse" all number smaller or equal to x1 is sumable, i.e. written as a sum of values in arr.

Next we want to argue that s should be larger than the sum of x1 and x2, which is the second largest value inside arr. This is because if s is smaller or equal to x1 + x2, we can find a set of values from arr, lets say set2, that sums to s - x1 since s - x1 < x1. We can observe that x1 is not in set2 because the sum of set2, s - x1, is smaller than x1, and a new set = set2 + {x1} sums to s. Therefore it contradicts the definition that s is not sumable.

We can further show that s should be larger than the sum of x1, x2, and x3, which is the third largest value inside arr. This is because if s is smaller or equal to x1 + x2 + x3, but larger than x1 + x2, we can find a set of values from arr, set3, that sums to s - x1 - x2 since s - x1 - x2 < x1. We can observe that x1 and x2 is not in set3 because the sum of set3, s - x1 - x2, is smaller than x1 or x2, and a new set = set3 + {x1, x2} sums to s. Therefore it contradicts the definition that s is not sumable.

Now by induction we could show that s is greater than the sum of x1, x2, ..., xn, which means that s is greater than the sum of arr. Therefore the smallest s possible is sum(arr) + 1.

For guess #2, we can prove it using the result from guess #1.

First, we rewrite guess #2 into a more mathematical term: Given the current array is "non-sparse" and the choice to add s = sum(arr) + 1 or an arbitrary p < s to the array, adding s is always the better choice, i.e. create an array that could "sum" more numbers.

By the result from guess #1, we know that for all x < s, x is sumable without adding any additional value. From there we could argue that by adding either s or p to arr, the new array, arr_new, is also "non-sparse". This is because either s is added, max(arr_new) = s which is sumable, and therefore all numbers smaller or equal to max(arr_new) is sumable, or p is added, max(arr_new) = max(p, max(arr)) < s, and by definition all numbers smaller or equal to max(arr_new) is sumable.

Now we use the result from guess #1 again which gives us the total sumable numbers of arr_new for adding p is sum(arr) + p < sum(arr) + s, the total sumable numbers for adding s. This means adding any p < s to arr would lead to less number being able to be sumed from arr_new, compared to adding s to arr. Therefore adding s to arr is always the better choice.

Now that we've proved these assumptions, its easy to write a code to solve this problem in O(log(n)): Start with an empty list, arr, and a counter, result, calculate the current s = sum(arr) + 1. If the first value in arr smaller or equal to s, remove it from nums and add it to arr. If the first value is larger than s, add s to arr and add 1 to result. Now recalculate s and repeat this process again until s > n. Return the counter, result.

## 3. Sliding Window

### 1438 Longest cont subarray w/ abs diff limit

***Difficult: /10***
***Interesting: /10***
***Educating: /10***

![1438](images/1438.png)

TODO

### 2009 Min number of operation to make array continuous

***Difficult: /10***
***Interesting: /10***
***Educating: /10***

![2009](images/2009.png)

TODO

## 4. Tree

### 1038 BST to Greater Sum Tree

***Difficult: 3/10***
***Interesting: 1/10***
***Educating: 6/10***

![1038](images/1038.png)

How to solve this problem using postorder by recursion is relatively easy. A good followup question would be how to solve it using simple stacks (or how to solve inorder/postorder using stacks in general).

(the algorithm below could be optimized using a stack of tuple, but here we assume only simple stacks, i.e. stacks of uniterable types, could be used)

We can start with two stacks, one have root in it, s1, and another empty, s2, and a sum calculator x = 0. While s1 is not empty, pop the top item in s1 as curr. If curr is a leaf node, update curr.val and x accordingly. If curr is equal to the top item in s2, update curr.val and x accordingly and pop the top itme in s2. In all other cases, push curr.left to s1 if its not none, push curr to s1 again, push curr to s2, and push curr.right to s1 if its not none. Return root at the end.

Here s2 serve as a path tracker or visited map. If a parent node is visited for the first time, we add its children to the stack as well as itself, without processing it. The next time this node appears, we process it and does not add anything to the stack to prevent infinite loop.
