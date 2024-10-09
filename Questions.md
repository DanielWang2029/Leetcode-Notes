# 1. Binary Search

When facing a problem which need to be solved within ```O(nlog(n))```, or ```O(log(n))``` appears in any forms, try binary search!

In general, binary search is a good try for questions with these characteristics:

1) ***The target value is bounded***

2) ***A direction towards the target value (lower or higher) could be determined given a non-target value***

3) ***It's not very expensive to check whether a value is target or not***

In addition, binary search is also very useful when trying to find things within a sorted scope. If the scope is not sorted and we need to perform multiple ```find``` operation, consider sorting the scope and use binary search for each ```find``` operation.

<br/>

## 1552 Magnetic force b/t balls

***Difficult: 7/10***

***Interesting: 5/10***

***Educating: 10/10***

<img src="images/1552.png" alt="Question 1552" width="600"/>

<br/>

The reason this question is very educating is that there's only one way to solve it within the required time frame: binary search.

Its obvious that the result for this question lies within ```[1, position[-1]]```. The observation most people overlooks, however, is that for any "required force" ```x <= result```, ```x``` should also be a valid distance to place ```m``` balls in positions because by definition ```result = max(x)``` for all ```x``` that's valid. These characteristics are signs for using binary search.

Now we need to think about the time to verify if a given distance between balls could be accomplished or not.

Given the target absolute distance ```x```, we could start with the first ball at ```position[0]``` and go through the positions until we've reached the first index ```i``` where ```position[i] >= position[0] + x```. We place the second ball at ```position[i]```. Now we start going through positions again beginning from index ```i + 1``` until we've reached the first index ```j``` where ```position[j] >= position[i] + x``` and place the third ball there. We continue this process until all ```m``` balls have been place, which means ```x``` as the "required force" could be done, or we run out of positions to place the balls, which means ```x``` as the "required force" could not be done. Either way, it takes ```O(n)``` time to verify whether a given distance is valid.

Therefore performing a binary search on the largest distance between balls would take ```O(n)``` for each verification and ```O(log(n))``` to find the boundary between valid and non-valid "required force", a total of ```O(nlog(n))```.

To reflect the general characteristics of binary search in the case of this question, the target value is the max distance between balls. Observe that

1) The target value is bounded by ```[1, position[-1]]```

2) The target value is the maximum of all valid values. This means if a given value ```x``` is valid, ```target >= x```; if a given value ```y``` is not valid, ```target < y```

3) It only cost one iteration of the entire position array, i.e. ```O(n)```, to check whether a given ```x``` is valid or not

Therefore when facing a question with similar characteristics, always gives binary search a try.

<br/>

## 1482 Min days to make n bouquets

***Difficult: 6/10***

***Interesting: 6/10***

***Educating: 7/10***

<img src="images/1482.png" alt="Question 1482" width="600"/>

<br/>

The common way to solve this problem is binary search. Think about the runtime to check if a given result would satisfy the criteria. It suppose to be ```O(n)```. Then we can perform a binary search on the result within the max and min of the given days and would produce a result within ```O(nlog(n))```. If the values of the days array is unbounded, we could sort the array first and perform binary search on the indexes of the array instead.

There's another approach to this problem: disjoint set. We could sort the days array and add each day sequentially while maintain a dictionary of the current status, i.e. [```key```, ```value```] represent ranges of blooming flowers. For each newly added position, we try to merge it into adjacent existing ranges, or create a new range itself, as well as maintaining the current max bouquets number, all in constant time. This way there's constant runtime for each position added and every position is added only once therefore the runtime for adding position is ```O(n)```. However, since we sorted the days array in the beginning, the overall runtime is also ```O(nlog(n))```.

<br/>

## 1508 Range sum of sorted subarray sums

***Difficult: 4/10***

***Interesting: 3/10***

***Educating: 10/10***

<img src="images/1508.png" alt="Question 1508" width="600"/>

<br/>

Its easy to come up with a solution with ```O(n^2log(n))``` using brutal force. However, if the sum of all values in ```nums``` is not terribly large, there's a solution with runtime ```O(nlog(sum(nums)))``` that could significantly reduces the time needed to solve this question.

The main idea behind this approach is binary search and sliding window. If there exist a function ```f``` that takes a sum ```x``` and returns how many subarrays in ```nums``` have a sum smaller or equal to ```x``` as well as the exact sum ```s``` of all values in such subarrays, we could perform a binary search on range ```[min(nums), sum(nums)]``` to get a sum, ```rightTarget```, such that ```f(rightTarget) = (right, rightSum)```. This means there's exactly ```right``` subarrays who's individual sums are smaller or equal to ```rightTarget``` and the sum of all values in such subarrays are ```rightSum```. Similarly, we could find a ```leftTarget``` such that ```f(leftTarget) = (left - 1, leftSum)``` and the result we're looking for is simply ```rightSum - leftSum```. The function ```f``` would be called ```O(log(sum(nums)))``` times during this process.

Now we need to figure out a way to come up with such function ```f``` such that each call of ```f``` takes ```O(n)``` time. To accomplish this, we want to utilize the trick in sliding window: We iterate through the array, maintaining a window from index ```start``` to ```end``` and keep track of all valid subarrays which ***ends on*** index ```end```. For example, if ```nums = [1, 1, 2, 3, 3]``` and the window has ```start = 1``` and ```end = 3``` then we are keeping track of these subarrays: ```[1, 2, 3]```, ```[2, 3]```, and ```[3]```. However, we won't be writing those arrays down in memory, but rather uses these variables to indirectly keep track of these arrays:

1) ```start```, the start index of the current window, initially ```0``` and updated for each ```end``` so that ```rowSum``` never exceeds ```target```.
2) ```rowSum```, the sum of ```nums[start]``` to ```nums[end]```, or ```nums[start:end + 1]```. Note that this is also the ***maximum sum*** of all the subarray we're keeping track of because it is the sum of the longest possible subarray in our window.
3) ```windowSum```, the sum of all values in subarrays we're keeping track of, i.e. who's sum is smaller or equal to ```target``` and ***ends on*** index ```end```. Note that the number of subarrays ```windowSum``` keeps track of is always equal to ```end - start + 1```.
4) ```result```, the number of subarray who's sum is smaller or equal to ```target``` that ***ends on or before*** index ```end```.
5) ```resultSum```, the sum of all values in subarrays who's sum is smaller or equal to ```target``` that ***ends on or before*** index ```end```.

For each index ```end``` from ```0``` to ```n```, we:

1) Extend our window from range ```[start, end - 1]``` to ```[start, end]```.
2) Add ```nums[end]``` to ```rowSum```, because our window now contains one more value ```nums[end]```.
3) Add ```(end - start + 1) * nums[end]``` to ```windowSum```, because for each previous subarray that ends on index ```end - 1```, we are extending it with ```nums[end]``` so that it ends on index ```end```, and we add the single element array of ```[nums[end]]``` as well.
4) If by moving ```end``` to the right makes ```rowSum``` larger than the input ```target```, we keep moving ```start``` to the right, update all variables accordingly until ```rowSum``` is smaller or equal to target:
   1) Subtract ```rowSum``` from ```windowSum```, because now our new window starts from ```start + 1``` and the only subarray we used to count that is not within our new range is the subarray from index ```start``` to index ```end```, which has the sum of ```rowSum```.
   2) Subtract ```nums[start]``` from ```rowSum```, because the maximum sum of all the subarray we're keeping track of is now no longer contains ```nums[start]```.
   3) Add ```1``` to ```start```.
5) Now we add ```end - start + 1``` to ```result```, because we have that many of subarrays which ends on index ```end``` and has a sum smaller or equal to ```target```.
6) Add ```windowSum``` to ```resultSum```, because ```windowSum``` now contains the sum of all values in subarrays that ends on index ```end``` and has a sum smaller or equal to ```target```.

Return ```(result, resultSum)``` after iterating through all possible index of ```end```.

There we have it, a function that takes ```O(n)``` to return the number of subarray and their sums, who's individual sum does not exceed ```target```.

Now back to the binary search part of our algorithm. Recall that we want to find ```rightTarget``` such that ```f(rightTarget) = (right, rightSum)```. However, there are circumstances where multiple subarrays will have the same sum and therefore not every count is guaranteed to be the result of our function ```f```. For example, there does not exist any input of ```target``` that the function ```f``` will return a ```count``` of ```10``` if ```nums = [1, 1, 2, 3, 3]```. This is because there's nine subarrays with sum of ```5``` or less and two subarrays with sum of ```6```, namely ```[1, 2, 3]``` and ```[3, 3]```, so possible outputs are ```9``` and ```9 + 2 = 11``` but not ```10```.

Note that this problem only emerges if there exists multiple subarray who's sum is the largest sum of all subarrays. As a result, we want to keep track of the duplicated largest sum of subarrays, or ```6``` in the above example, and the difference between the actual count and the desired count, or ```11 - 10 = 1``` in the above example, and subtract the product of those two values from our ```resultSum``` output. Therefore in our binary search function ```bs(count)``` where ```count``` is our desired number of subarrays, we should return ```resultSum - mid * (result - count)``` given the search result is ```mid = left = right``` and ```f(mid) = (result, resultSum)```. In the above example, ```count = 10```, ```mid = 6```, ```f(6) = (11, 36)```, and ```bs(10) = 36 - 6 * (11 - 10) = 30```.

Another small detail here is that the actual count should always be ***larger or equal*** to the desired count because only then the largest sum of subarrays is ```mid```. In the above example, if the search result is ```mid = 5``` (actual count = ```9```) instead of ```mid = 6``` (actual count = ```11```), we would have subtracted ```5 * (10 - 9)``` from ```resultSum```  and got a wrong answer. To avoid this situation, we need to make sure that ```mid``` is updated by ```mid = (left + right) // 2```, and when the actual count is larger/smaller than desired count, we set ```right = mid``` and ```left = mid + 1```. Using the update method of ```mid = (left + right) // 2 + 1``` would causes troubles, either making the actual count smaller to the desired count, or resulting in binary search stuck in an infinite loop in certain circumstances.

Lastly, note that this approach only works if all the values are non-negative. If negative number exists, theoretically we could add ```-min(nums)``` to all values in ```nums``` to avoid failure. However, this will increase ```sum(nums)``` and therefore increase the runtime for the entire algorithm. So further analysis should be done case by case to determine which approach is better.

<br/>

# 2. Dynamic Programming

## 956 Tallest billboard

***Difficult: 9/10***

***Interesting: 7/10***

***Educating: 9/10***

<img src="images/956.png" alt="Question 956" width="600"/>

<br/>

For this question, I cheated a little bit and saw the topic for this question is dynamic programming and therefore know the solution involves some kind of dp, which makes it less difficult than it supposed to be. Nevertheless, its still an hard and interesting dp question to solve.

***Dynamic programming is somewhat like mathematical induction, we want to solve a bigger question incrementally using the results from previous derived smaller questions.***

Using this dynamic programming mindset, we want to first determine how could we divide our big question into smaller questions. In the case of this question, we have several options:

1. Increment on the height. Remember some info about the billboard of height in ```[1, h]``` and use that to determine whether we could support a billboard of height ```h + 1``` using all tiles.

2. Increment on the tiles used. Remember some info about the billboards we could support using the first ```[1, i]``` tiles and use that to determine the billboards we could support using the first ```i + 1``` tiles.

3. Combine the two thoughts above and increment on both the height as well as the tiles used. Remember some info about the support of billboards of height in ```[1, h]``` using the first ```[1, i]``` tiles and use that to determine whether we could

   1. support a billboard of height ```h + 1``` using the first ```i``` tiles, or

   2. support a billboard of height ```h``` using the first ```i + 1``` tiles.

   The reason its ***or*** but rather ***and*** here is that its easy to figure out the base cases in our 2d dynamic programming for this question, i.e. its easy to figure out whether we could support a billboard of height ```1``` using the first ```[1, i]``` tiles and whether we could support a billboard of height ```[1, h]``` using the first ```1``` tile. Therefore as long as we could increment our dp array in one direction using the results already derived (also 2d), we are good to go. Think about it as we already have the two axis of our dp array and we only need the ability to move in one direction to fill in all the values in that array.

After some thoughts we could find out none of the methods works using normal dp tricks. However, the second option gives us some insight about how we could proceed further by expanding the normal 1d dp into a 2d dp (which is different from the third option).

Think about the information needed on the first ```[1, i - 1]``` tiles for us to determine if we could support a billboard with height ```h``` using the first ```[1, i]``` tiles. There's actually only two scenarios:

1. We do not use tile ```i``` to support our billboard. This means whether we could support the billboard or not does not change with the addition of tile ```i```, i.e. ```f(h, i) = f(h, i - 1)``` for some dp function ```f```. No info is needed to calculate this given the results from previous sub-problems (```f(h, i - 1)```).

2. We use tile ```i``` to support our billboard. This means we should be able to build two pillars that have height of ```h``` and ```h - rods[i]```. Additional info about whether we could support two pillars with difference ```rods[i]``` from previous sub-problems is needed.

From there, we could find out that the info we needed to inherit from previous sub-problems is whether we could support two pillars with difference ```d```, which has a upper bound of ```sum(rods)```, instead of the original setup where info is only given on two pillars with the same height.

As a result, we could setup our 2d dp array ```arr``` like this:

1. ```arr``` has size ```len(rods) * sum(rods)``` and is initialized as all ```-1```s.

2. ```arr[i][h]``` represents the maximum height of the higher pillar if we could use first ```i``` tiles to build two pillars that have a height difference of ```h```, or ```-1``` if we could not build such pillars.

3. The base case is given by ```arr[0][0] = 0``` and ```arr[0][rods[0]] = rods[0]```.

Since we've added an additional dimension in our dp array, we need to rethink about the scenarios and how to update our dp array ```arr``` accordingly:

1. Same as before, we do not use tile ```i``` and therefore ```arr[i][h] = arr[i - 1][h]```, meaning the height of the higher pillars we could build using the first ```i``` tiles where two pillars have a height difference of ```h``` is the same as the height of the higher pillars we could build using the first ```i - 1``` tiles.

2. We use tile ```i``` in the lower pillar. This means the height of the higher pillar does not change and its the same as the higher pillar we could build using the first ```i - 1``` tiles where two pillars previously have a height difference of ```h + rods[i]```. Therefore we have ```arr[i][h] = arr[i - 1][h + rods[i]]```.

3. We use tile ```i``` in the higher pillar. This means the previous difference between the current higher pillar and the current lower pillar is ```h - rods[i]```. There's two sub-cases here:
   1. If ```h``` is larger than ```rods[i]```, we are adding tile ```i``` onto the pillar that is previously higher. Therefore ```arr[i][h] = arr[i - 1][h - rods[i]] + rods[i]``` because ```rods[i]``` is the change of height for the higher pillar.
   2. If ```h``` is smaller than ```rods[i]```, we are adding tile ```i``` onto the pillar that is previously lower, making it the higher pillar during this operation. Let ```x``` is the height of the pillar that is previously higher, then subsequently ```x - (rods[i] - h)``` is the height of the pillar that is previously lower. The change in height of the higher pillar is therefore ```(x - (rods[i] - h) + rods[i]) - x```, which is ```h```, giving us ```arr[i][h] = arr[i - 1][rods[i] - h] + h```.

   Merging two equations gives us ```arr[i][h] = arr[i - 1][abs(rods[i] - h)] + min(rods[i], h)```.

Together, we have our recurrent relationship: ```arr[i][h] = max(arr[i - 1][h], arr[i - 1][h + rods[i], arr[i - 1][abs(rods[i] - h)] + min(rods[i], h))```. The result we are looking for, after filling all values in our dp array ```arr```, would be ```arr[len(rods)][0]```.

<br/>

## 714 Best time to buy and sell stock w/ fee

***Difficult: 7/10***

***Interesting: 5/10***

***Educating: 9/10***

<img src="images/714.png" alt="Question 714" width="600"/>

<br/>

It's easy to think about dynamic programming as solution for this question. My initial thought was to keep track of the max profit for prices from index ```0``` to ```k``` where max profit is either the previous max profit at index ```k - 1```, meaning sale were not made on index ```k```, or a prior max profit at index ```i - 1``` plus the profit from buying at index ```i``` and selling at index ```k``` where ```i``` could range from ```0``` to ```k - 1```. But I soon faced the problem where I do not know when the stock is brought in constant time, i.e. do not know what ```i``` should be, leading to a ```O(n)``` runtime to search for optimal ```i``` for each iteration, totalling a ```O(n^2)``` runtime for the entire algorithm.

However, if we think about the states we are in at each index, there's actually only two: either we are holding a stock that's already bought and ready to be sold, or we are not holding any stock and are available to buy a stock. These two states are different but interchangeable based on the current stock price ```p = prices[i]```:

Let ```x``` by the current max profit for state where we're not holding stocks and ```y``` be the current max profit minus the buying price for the stock we're currently holding for state where we're holding a stock. The initial values are ```0``` for ```x``` and ```-prices[0]``` for ```y```. For each iteration, the state where we're not holding stocks could be achieved by either started with nothing in hand and does not buy stock at price ```prices[i]```, or started with a stock in hand and sells it at price ```prices[i]```. This gives us ```x_new = max(x, y + prices[i] - fee)```. Similarly, we could achieve the state where we're holding a stock by either started with a stock in hand and does not sell the stock at price ```prices[i]```, or started with nothing in hand and buys the stock at price ```prices[i]```. This gives us ```y_new = max(y, x - prices[i])``` (we assume ```fee``` only occur during a stock sell).

Using this algorithm, the answer we're looking for is ```x``` after iterating through all prices. Here although ```y``` has nothing to do with the final result, it serves as a crucial intermediate step that supports the generation of ```x``` for each iteration.

<br/>

# 3. Sliding Window

## 1438 Longest cont subarray w/ abs diff limit

***Difficult: 5/10***

***Interesting: 4/10***

***Educating: 9/10***

<img src="images/1438.png" alt="Question 1438" width="600"/>

<br/>

The original method I used and the sliding window method is quite similar in terms of the general approach of using two pointers representing a subarray to traverse the entire array while keeping track of the absolute difference. Both of these methods would increase right pointer incrementally and move the left pointer rightward until the max absolute difference of the subarray is within the boundary.

However, the later method uses a better storage system that reduces the runtime to ```O(n)``` instead of ```O(nlog(n))```. My initial method uses two heaps, one max heap and one min heap, and a counter dictionary to keep track of the count of values as well as the max absolute difference within the subarray. This requires maintaining heaps which cost ```O(log(n))``` for each operation. Instead, we could accomplish the same goal using two deques, a max deque and a min deque.

Whenever we move the right pointer to the right, we want to insert the new value ```x``` into the two deques so that ```x``` is at the top of the deques and all other values are larger/smaller than ```x``` in maxstack/minstack. Notice that by doing so, the deques would actually be ordered.

***This is a common practice to keep track of the largest/smallest value for a changing array.***

Once the deques are updated, we keep moving the left pointer to the right, remove any bottom values of the deques, namely the largest and smallest value within the subarray, that is equal to the discarded value, until the bottom of the two deques have a absolute difference smaller or equal to the limit.

This method is valid because the deque, take the max deque as an example, is maintained in a way where its sorted in descending order from bottom to top, and each value, if in the deque, is the largest value from the previous max/min (the value one place lower than it in the deque) to the current right end of the array. Therefore when we pop the bottom of the maxdeque/mindeque, the new bottom value is the new max/min of the subarray. A tricky edge case would be duplicate values in the subarray, in which case we keep all the duplicates in the deque.

<br/>

## 2009 Min number of operation to make array continuous

***Difficult: 6/10***

***Interesting: 6/10***

***Educating: 7/10***

<img src="images/2009.png" alt="Question 2009" width="600"/>

<br/>

There's two different way to solve this question: sliding window, and perhaps surprisingly, binary search.

First, we want to sort all ***distinct*** elements of the array in ascending order as the order of the elements has no effect on the result and each element is only utilized once, making duplicated elements useless. This takes ```O(nlog(n))``` time and we will do this regardless of which solution we use.

For the sliding window approach, we keep track of a window where the max difference of elements in the window is smaller or equal to the size of the array, i.e. ```window[-1] - window[0] < len(nums)```. We do this by keeping track of a left and a right pointer indicating the window position. We move right pointer to the right as long as the criteria is satisfied. If the criteria is broken, we move our left pointer to the right until the criteria is met again. During the sliding of window, we record the size of valid windows, ```size```, which is the number of distinct elements within range of ```[window[-1], window[-1] + len(nums) - 1]```. The result is hence ```len(nums) - max(size)``` for all valid windows, because the bigger a window is, the smaller number of elements outside of the required range is, and therefore less operation is required.

For the binary search approach, we are still trying to find corresponding windows and calculate their size. However, notice that for each element in this distinct sorted array there's a valid window that starts at that element, and the right pointer is the rightmost element that satisfy the criteria ```window[-1] - window[0] < len(nums)```. Hence we can iterate the distinct sorted array, using each elements as the start of the window, and get the window boundary by using binary search on the right pointer, which takes ```O(log(n))``` for each iteration. Then we can get the minimum number of operation using similar logic on the length of ```nums``` and the maximum size of all valid windows.

Both approach take ```O(nlog(n))``` time due to the initial distinct sort.

<br/>

# 4. Tree

## 1038 BST to greater sum tree

***Difficult: 3/10***

***Interesting: 1/10***

***Educating: 7/10***

<img src="images/1038.png" alt="Question 1038" width="600"/>

<br/>

How to solve this problem using postorder by recursion is relatively easy. A good followup question would be how to solve it using simple stacks (or how to solve inorder/postorder using stacks in general).

(the algorithm below could be optimized using a stack of tuple, but here we assume only simple stacks, i.e. stacks of uniterable types, could be used)

We can start with two stacks, one have ```root``` in it, ```s1```, and another empty, ```s2```, and a sum calculator ```x = 0```. While ```s1``` is not empty, pop the top item in ```s1``` as ```curr```. If ```curr``` is a leaf node, update ```curr.val``` and ```x``` accordingly. If ```curr``` is equal to the top item in ```s2```, update ```curr.val``` and ```x``` accordingly and pop the top itme in ```s2```. In all other cases, push ```curr.left``` to ```s1``` if its not none, push ```curr``` to ```s1``` again, push ```curr``` to ```s2```, and push ```curr.right``` to ```s1``` if its not none. Return ```root``` at the end.

Here ```s2``` serve as a path tracker or visited map. If a parent node is visited for the first time, we add its children to the stack as well as itself, without processing it. The next time this node appears, we process it and does not add anything to the stack to prevent infinite loop.

<br/>

## 1395 Count number of teams

***Difficult: 5/10***

***Interesting: 4/10***

***Educating: 9/10***

<img src="images/1395.png" alt="Question 1395" width="600"/>

<br/>

After some consideration, we could arrive at a ```O(n^2)``` solution by examining the middle soldier of each team:

For each index ```i```, we calculate how many ascending teams can be formed using ```rating[i]``` as the middle soldier by counting the number of soldiers who have a rating lower than it to the left, ```lowerLeftCount```, and multiply it with the number of soldiers who have a rating hight than it to the right, ```higherRightCount```. For descending teams, we could using ```higherLeftCount``` times ```lowerRightCount```, all done within ```O(n)``` time for each ```i```.

However, there's different approach which utilizes ***Binary Indexed Tree (Fenwick Tree)*** and have a runtime of ```O(nlog(max(rating)))```. It will significantly reduces the runtime given that the maximum rating for the soldiers are not too crazy.

To recap, binary index trees are binary search complete trees who's inorder traversal gives the values in ascending index (```0``` to ```n```). It is implemented using just an array and it uses the binary representation of indexes to traverse the tree. Each node of the tree contains the the value of itself plus the cumulative sum of values of its left tree. My own implementation of Binary Indexed Tree could be found [here](https://github.com/DanielWang2029/Python-and-Algorithms/blob/master/Data_Structures_and_Algorithms/Binary_Index_Tree.py).

One of the great property of binary index trees is that getting the cumulative sum of values/frequencies takes ```O(log(n))``` time where ```n```  is the size of the tree. So to calculate the number fo ascending teams, we could create two binary index tree, ```lowerTree``` and ```higherTree```, of size ```max(rating)```: ```lowerTree``` is initially empty and get expanded at each iteration to store the count of number of soldiers who's index is smaller and value is lower, while ```higherTree``` initially contains all soldier's value and get reduced at each iteration to the count of number of soldiers who's index in higher and value is higher. For each iteration of the soldiers with value ```v```, we add ```lowerTree[v] * higherTree[v]``` to the result, add ```1``` for ```lowerTree[v]``` by calling the ```increment_value()``` function (which will visit more than just the node with index ```v``` for ```lowerTree``` and takes ```O(log(max(rating))``` time due to the setup of binary index trees), and reduce ```1``` for ```higherTree[v]``` also by calling the ```increment_value()``` function.

Do the same (with a little different update method) to calculate the descending teams and return the sum of two results.

<br/>

# 5. Graph

## 1579 Remove max # of edges to keep graph fully traversable

***Difficult: 8/10***

***Interesting: 7/10***

***Educating: 8/10***

<img src="images/1579.png" alt="Question 1579" width="600"/>

<br/>

The typical approach to this questions is disjoint set. The main idea is to count the number of edges required to connect all vertices using two separate disjoint set for Alice and Bob, prioritizing shared edges, and return the number of edges minus the required number of edges.

Let ```ds```, ```ds1```, ```ds2``` be arrays with length ```n``` representing different disjoint sets. Let ```result``` be the number of edges required to make graph fully traversable for Alice and Bob. We iterate ```edges``` for the first time, only at edges ```[type, u, v]``` that have ```type = 3```, and check if ```u``` and ```v``` are in the same group by checking if their roots are the same. If they are not, we union them together in ```ds``` and add ```1``` to ```result```. This create a disjoint set ```ds``` that represents the connected groups for both Alice and Bob because the edges are shared.

Now we let ```ds1``` and ```ds2``` equal to ```ds```. We then iterate ```edges``` for the second time, only at edges ```[type, u, v]``` that have ```type = 1```, and check if ```u``` and ```v``` are in the same group. If not, we union them together in ```ds1``` and add ```1``` to ```result```. This create a disjoint set ```ds1``` that represents the connected groups for Alice only. If any of the vertices are not connected to root (vertex ```0```), i.e. there exist more than one group in ```ds1```, we know that this graph is not fully traversable for Alice even with all available edges and we should return ```-1```.

Similar to how we deal with Alice using ```ds1```, we iterate ```edges``` one more time, do the same for Bob using ```ds2```, and return ```-1``` if the graph is not fully connected with all available edges. Otherwise, we return ```len(edges) - result``` because all edges except the required to make graph fully traversable are the edges that can be removed.

On a side note, there exists ```O(n)``` solutions. In fact, my first successful submission takes ```O(n)``` time in theory, but in reality it takes way more time than ```O(nlog(n))``` solutions and even I cannot easily recreate the logic behind it.

<br/>

## 2392 Build a matrix with condition

***Difficult: 8/10***

***Interesting: 7/10***

***Educating: 10/10***

<img src="images/2392.png" alt="Question 2392" width="600"/>

<img src="images/2392-2.png" alt="Question 2392-2" width="600"/>

<br/>

Take a closer look at this question and you'll find its actually a graph sorting question: 

If we find two sequences of numbers representing the horizontal and vertical order where each satisfies that ```l``` comes before ```r``` for all ```(l, r)``` in row or column conditions, we can construct such matrix with ease. For example, for any given two sequences of 1, 2, 3, we can use the index of a given number from the first sequence, ```i```, and the index of that number from the second sequence, ```j```, to locate its position in the resulting matrix: ```[i, j]```. For example, sequences 3-1-2 and 3-2-1 gives us the matrix from example 1 in the question description.

It's also related to graph because each ```r``` could have several ```l``` depended on it and therefore making the requirements/prerequisites a graph.

Therefore it is what's called a topological sorting question and we could use ***Kahn's Algorithm*** to solve this.

Kahn's Algorithm finds a linear ordering of vertices for a ***Directed Acyclic*** graph, such that for every directed edge ```u``` -> ```v```, vertex u comes before v in the ordering. The algorithm works by repeatedly finding vertices with no incoming edges, removing them from the graph, and updating the incoming edges of the remaining vertices. This process continues until all vertices have been ordered.

To initialize the algorithm, we first need to find the ***in-degree*** of each node/number. We calculate the number of incoming edges to each node. Iterate through all the edges in the graph and increment the in-degree of the destination node, or ```r```, for each edge.

From there, we create a queue filled with all 0 in-degree nodes (i.e. nodes w/ 0 incoming edges) and start subtracting the number of incoming edges for their children, then put all nodes with 0 incoming edges to the queue and repeat until the queue is empty. The sequence we pop from the queue is the topological sort that we're looking for.

***Time complexity of Kahn's algorithm: ```O(V + E)```.***

***Space complexity of Kahn's algorithm: ```O(V)```.***

Node that Kahn's algorithm only works on ***directed*** and ***acyclic*** graph. If a graph is not directed, we won't be able to know if an edge from ```u``` to ```v``` means ```u``` must comes before ```v``` or the other way around. If a graph have a cycle within it, then the nodes on this cycle will never reach the state where they have 0 incoming edges and thus would not be included in our final output. (This phenomenon could actually be used to detect cycle within a directed graph!)

<br/>

## 1334 Find the city w/ min # of neighbor at a threshold distance

***Difficult: 7/10***

***Interesting: 6/10***

***Educating: 10/10***

<img src="images/1334.png" alt="Question 1334" width="600"/>

<br/>

There 4 ways to solve this question: Dijkstra's Shortest Path Algorithm, Floyd–Warshall algorithm, Bellman–Ford Algorithm, and Shortest Path Faster Algorithm (SPFA).

1) ***Dijkstra's Shortest Path Algorithm***

Dijkstra's Algorithm finds the Shortest Path Tree, SPT, or the shortest path to any other nodes for a given root node in a non-negatively-weighted graph. It starts with root, update and keeps track of the shortest distance/path for each children of the current node, and choose the next node to visit by selecting the one with the shortest distance to root, until all nodes are visited.

Here's a short description of the implementation of Dijkstra's algorithm: We start by creating a minheap ```h```, an adjacency list ```adj```, and a visited array ```v```. ```h``` starts with just one element: ```(0, root)```, ```adj``` starts with 0 at ```adj[root]``` and positive infinity at all other spots, and ```v``` starts with all```False```. While ```h``` is not empty, we pop the min element ```curr``` and mark it as visited on ```v```. Then for all of its children, we find its current path distance ```adj[curr] + w_child```. If it is smaller than ```adj[child]```, we use it to update ```adj``` and heappush ```(adj[child], child)``` to ```h```. Repeat this process until the heap is empty (or all nodes are visited, minor differences but both correct). Node that in this approach multiple instances of same vertex is allowed in the heap and we only consider the instance with minimum distance.

If SPT is what expected as output, just keep track of an additional dictionary where keys are child nodes and values are parent nodes. Update dictionary accordingly when ```adj``` got updated and reconstruct the tree using this dictionary at the end.

***Time complexity of Dijkstra's Algorithm: ```O(Elog(V))```.*** A better runtime of ```O(E + Vlog(V))``` could be accomplished by using the [Fibonacci Heaps](https://www.youtube.com/watch?v=6JxvKfSV9Ns). My own implementation of Fibonacci Heaps could be found [here](https://github.com/DanielWang2029/Python-and-Algorithms/blob/master/Data_Structures_and_Algorithms/Fibonacci_Heap.py).

***Space complexity of Dijkstra's Algorithm: ```O(V)```.***

The reason Dijkstra's Algorithm cannot be used on negatively-weighted graphs is because it assumes that once a node is marked as visited, its distance is finalized and will not change. This assumption breaks when negative weights are introduced and a shorter path could be later found out for an already visited node.

2) ***Bellman–Ford Algorithm***

Bellman-Ford algorithm is also guaranteed to find the shortest path in a graph. The only limit for Bellman-Ford is that there cannot exist a negative cycle in the graph. Compared to Dijkstra's algorithm, Bellman-Ford is slower but is capable of handling ***directed*** graphs with negative edge weights. Since undirected graphs are essentially directed graph with additional swapped edges, an undirected graph with negative edges would result in negative cycles in the equivalent directed graph hence cannot be analyzed by Bellman-Ford.

The principle idea behind Bellman-Ford is to relax edges. We start by maintaining a distance array/ adjacency list ```adj``` just like in Dijkstra. In each relaxation, we go through each edge ```(u, v)``` and see if going from ```u``` to ```v``` lead to a shorter distance for ```v```, i.e. whether ```adj[u] + w < adj[v]```. If so, we update the adjacency list accordingly using ```adj[v] = adj[u] + w```. We repeat this relaxation for ```V - 1``` time and return the adjacency list ```adj```. The reason for ```V - 1``` is that in worst-case scenario, a shortest path between two vertices would go through every vertices and have at most ```V - 1``` edges, given that no negative cycles exist in the graph therefore no loops are formed during the path.

***Time complexity of Bellman-Ford Algorithm: ```O(VE)```.***

***Space complexity of Bellman-Ford Algorithm: ```O(V)```.***

We can also use Bellman-Ford Algorithm to detect negative cycles: After ```V - 1```th relaxation, we want to relax all edges one more time, reaching ```V``` iterations. If the update criteria ```adj[u] + w < adj[v]``` successes for any edges, then there exists negative cycles in the graph. This is because during the ```V - 1```th relaxations, we presume that each vertex is traversed only once. However, the reduction of distance during the ```V```th indicates that we revisited at least one vertex resulting a loop in our traversed path, which indicates a negative cycle in the graph.

3) ***Shortest Path Faster Algorithm (SPFA)***

The shortest path faster algorithm is based on Bellman-Ford algorithm, but in SPFA, a queue of vertices is maintained and a vertex is added to the queue only if that vertex is relaxed. This process repeats until no more vertex can be relaxed.

We start by creating a queue ```q``` and a set ```s``` containing all the elements in ```q```, both having the root vertex in it initially, in addition to the adjacency list ```adj```. while the queue is not empty (which is equal to the set being not empty), we pop the leftmost item ```curr``` in ```q``` and delete it from ```s```. For every outgoing edges ```(curr, v)``` of ```curr```, we calculate whether the update criteria ```adj[curr] + w < adj[v]``` is met. If so, we update the adjacency list through ```adj[v] = adj[curr] + w``` and add ```v``` to ```q``` and ```s``` if ```v``` is not already in ```s``` (consequently not in ```q```). Return ```adj``` once ```q``` is empty.

***Time complexity of SPFA: ```O(VE)```.***

***Space complexity of SPFA: ```O(V)```.***

4) ***Floyd–Warshall algorithm***

Floyd-Warshall algorithm is also an algorithm for finding the shortest paths in a directed, weighted graph without negative cycles. Its core is a recursive function ```f``` which takes three inputs, ```i```, ```j```, ```k```, and returns the length of the shortest path from ```i``` to ```j``` using vertices only from set ```{1, 2, ..., k}``` as intermediate points on the way. Given this function, the result we're looking for is ```f(start, end, V)``` which gives the shortest path length from ```start``` to ```end``` using all ```V``` vertices.

For the recursive relation, observe that ```f(i, j, k)``` should be smaller or equal to ```f(i, j, k - 1)``` because the latter has less available vertices for traversal. If ```f(i, j, k)``` is indeed less than  ```f(i, j, k - 1)```, then there must exist a path that goes through ```k``` which is shorter than any path that does not go through ```k```. Therefore we can decompose the shortest path into two parts:

(1) a path from ```i``` to ```k``` using only vertices from ```{1, 2, ..., k - 1}```.

(2) a path from ```k``` to ```j``` using only vertices from ```{1, 2, ..., k - 1}```.

Both of these sub-paths have to be the shortest among all paths that satisfy the criteria, giving us this recursive formula:

```f(i, j, k) = min(f(i, j, k - 1), f(i, k, k - 1) + f(k, j, k - 1))```

The base case of this recursive function is given by:

```f(i, j, 0) = w(i, j)```

where ```w(i, j)``` is the weight of the edge connecting ```i``` and ```j```, positive infinity if such edge does not exist.

In terms of implementing Floyd-Warshall algorithm, we could use dynamic programming on a two dimension ```dp``` array. First we initialize the ```dp``` array where ```dp[i][j]``` equals ```w(i, j)``` if the edge ```(i, j)``` exists otherwise positive infinity, and ```dp[v][v] = 0``` for all vertices. Here ```dp[i][j]``` represents the value of ```f(i, j, 0)```. Then for every ```k``` from ```1``` to ```V```, we iterate through each pair of ```(i, j)``` and update ```dp[i][j]``` to ```dp[i][k] + dp[k][j]``` if the later is smaller. This is equivalent to finding the value of ```f(i, j, k)``` using ```f(i, k, k - 1)``` and ```f(k, j, k - 1)``` in the recursive relation. The ```dp``` table after ```V```th iteration of ```k``` is the result we're looking for, where ```dp[i][j]``` represents the length of shortest path from ```i``` to ```j```, or ```f(i, j, V)```.

Note that for certain values of ```i```, ```j```, and ```k```, ```dp[i][k]``` or ```dp[k][j]``` might already been updated in the same iteration of ```k``` before ```dp[i][j]```, meaning the value they represents are actually ```f(i, k, k)``` and ```f(k, j, k)``` rather than ```f(i, k, k - 1)``` and ```f(k, j, k - 1)```. However, observe that if ```i = k``` or ```j = k```, ```f(k, j, k - 1) = f(k, j, k)``` and ```f(i, k, k - 1) = f(i, k, k)``` because a path from or to ```k``` will not be using itself as an intermediate vertex. Therefore the recursive formula still holds.

***Time complexity of Floyd-Warshall algorithm: ```O(V^3)```.***

***Space complexity of Floyd-Warshall algorithm: ```O(V^2)```.***

Similar to Bellman-Ford Algorithm, Floyd-Warshall algorithm can also be used to detect negative cycles: If a negative cycle exists, then the length of the shortest path from a vertex on that cycle to itself would be less than the initial value of ```0```. Therefore we can simply inspect the diagonal of the ```dp``` matrix during any time of the algorithm, and the presence of a negative number indicates that the graph contains at least one negative cycle.

<br/>

## 2699 Modify graph edge weight

***Difficult: 10/10***

***Interesting: 8/10***

***Educating: TODO/10***

<img src="images/2699.png" alt="Question 2699" width="600"/>

<img src="images/2699-2.png" alt="Question 2699-2" width="600"/>

<img src="images/2699-3.png" alt="Question 2699-3" width="600"/>

<br/>

TODO

<br/>

# 6. Array

## 215 Kth largest element in array

***Difficult: 1/10***

***Interesting: 3/10***

***Educating: 8/10***

<img src="images/215.png" alt="Question 215" width="600"/>

<br/>

It's almost too easy to arrive at a solution with sorting (or heap) The education part of this question, however, lies in the solution that does not uses sorting and takes ```O(n)``` time on average.

TODO(quick select, median of median)

<br/>

# 7. String

## 28 Find first Occurrence index in a string 

***Difficult: 1/10***

***Interesting: 7/10***

***Educating: 9/10***

<img src="images/28.png" alt="Question 28" width="600"/>

<br/>

TODO(kmp table, Rabin-Karp algorithm (hashing))

<br/>

# 8. Union Find

## 959 Regions cut by slashes

***Difficult: 4/10***

***Interesting: 5/10***

***Educating: 6/10***

<img src="images/959.png" alt="Question 959" width="600"/>

<br/>

This is a classic union find question on first glance. It's how to define a base element that's worth discussing.

My initial approach is to divide each block into four square subblocks: upperleft, upperright, lowerleft and lowerright. To deal with slashes, my approach lets them block two subblocks on the diagonal, leaving the other diagonal free to union/join with subblocks of adjacent blocks. This is somewhat counterintuitive as the subblocks are not divided along the slashes. A more intuitive approach is to divide each block into four triangular subblocks: up, down left and right. If slashes are present, it's guaranteed to have two whole subblocks on each side of the slashes covering two sides of the block, and we can union/join them together along with subblocks of adjacent blocks.

Both of these approaches have ```4n^2``` subblocks as base elements therefore the runtime is ```O(4n^2log(4n^2))```. However, there exist a better solution:

Think about when do we know for sure that an additional region is formed. It is when we draw a slash and found out that it finishes a loop of edges, i.e. it connects two existing and already connected edges. The takeaway from this is that we can treat points, not blocks, as base elements in our union find solution and we union/join points to represent connected edges, rather than union/join blocks to represent connected region.

Specifically, we could create a new grid of points ```g``` of size ```(n + 1) * (n + 1)``` and initialize the border of the grid (every ```g[i][j]``` where ```i = 0``` or ```j = 0``` or ```i = n``` or ```j = n```) to be in a same group while every inner point of the grid has its own group. Now we iterate through all the slashes and union/join ```g[i][j]``` and ```g[i + 1][j + 1]``` if a slash, i.e. ```\```, is found on ```grid[i][j]```, or union/join ```g[i + 1][j]``` and ```g[i][j + 1]``` if a backslash, i.e. ```/```, is found on ```grid[i][j]```. If before joining we notice that the two points are already connected and are in the same group, we know that we've closed a circle of edges and we want to add ```1``` to the result. Return the result after every slashes are visited.

This solution have ```(n + 1)^2``` points as base elements therefore the runtime is ```O((n + 1)^2log(n + 1)^2)```, which is the same big-O notation of runtime as previous approaches but runs noticeably faster in practice.

<br/>

# 9. Backtracking

## 40 Combination sum II

***Difficult: 6/10***

***Interesting: 4/10***

***Educating: 7/10***

<img src="images/40.png" alt="Question 40" width="600"/>

<br/>

This question could be solved using backtracking. Backtracking incrementally builds candidates to the solutions and abandons a candidate (backtracks) as soon as it determines that this candidate can't lead to a final solution.

***In fact, backtracking can be effectively used to solve questions that require the generation of all the possible combinations recursively.***

In this question, we can sort the ```candidates``` first and keep track of a list of values, initialized to contain the smallest value, while attempting to add additional values from ```candidates```. If adding a new value takes the sum of our list over ```target```, we do not add that value and pop one value from the end of our list instead. Then we attempt to add a value that is bigger to the popped one (skip duplicated values) from the sorted ```candidates```. If adding a new value makes the sum of our list equal ```target```, we write down the current list along with the new value into ```result``` and again pop one value from the end of our list and attempt to add a value that is bigger. If adding a new value still results in smaller list sum than ```target```, we attempt to add the next value in our sorted ```candidates``` (could be the same value if duplicated). We break from this iteration after no values could be added to the list and no values could be further popped (i.e. we've attempted all values as the first value in our list).

<br/>

# 10. Math

## 330 Patching array

***Difficult: 9/10***

***Interesting: 10/10***

***Educating: 1/10***

<img src="images/330.png" alt="Question 330" width="600"/>

<br/>

Lets first define a simple case where the array is "non-sparse", meaning all numbers within the range of the values, min to max, could be written into a sum. For example, [1, 2, 3, 5, 9] would be a "non-sparse" array where [1, 2, 4, 9, 15] is not because 8 cannot be written into a sum.

From here, It's reasonable to guess that:

1) For a given "non-sparse" array, the next value to be patched/added (if needed) is the sum of the array plus 1. In other words, given a "non-sparse" array ```arr```, the smallest number that cannot be written as the sum of current arr values is the ```sum(arr) + 1```

2) Adding the smallest missing/unreachable/unsumable number is better than adding any number that is smaller than it.

Given these guesses, it's easy to derive the final solution. The hard part, however, lies within the proof of these assumptions.

For guess #1, we can prove it by induction:

Consider the smallest number that cannot be written as a sum of current values, which lets call ```s```.

First we want to argue that ```s``` should be larger than the largest value inside ```arr```, which lets call ```x1```. This is because by definition of "non-sparse" all number smaller or equal to ```x1``` is sumable, i.e. written as a sum of values in ```arr```.

Next we want to argue that ```s``` should be larger than the sum of ```x1``` and ```x2```, which is the second largest value inside ```arr```. This is because if ```s``` is smaller or equal to ```x1 + x2```, we can find a set of values from ```arr```, lets say ```set2```, that sums to ```s - x1``` since ```s - x1 < x1```. We can observe that ```x1``` is not in ```set2``` because the sum of ```set2```, ```s - x1```, is smaller than ```x1```, and a new ```set = set2 + {x1}``` sums to ```s```. Therefore it contradicts the definition that ```s``` is not sumable.

We can further show that ```s``` should be larger than the sum of ```x1```, ```x2```, and ```x3```, which is the third largest value inside ```arr```. This is because if ```s``` is smaller or equal to ```x1 + x2 + x3```, but larger than ```x1 + x2```, we can find a set of values from ```arr```, ```set3```, that sums to ```s - x1 - x2``` since ```s - x1 - x2 < x2```. We can observe that ```x1``` and ```x2``` is not in ```set3``` because the sum of ```set3```, ```s - x1 - x2```, is smaller than ```x1``` or ```x2```, and a new ```set = set3 + {x1, x2}``` sums to ```s```. Therefore it contradicts the definition that ```s``` is not sumable.

Now by induction we could show that ```s``` is greater than the sum of ```x1```, ```x2```, ..., ```xn```, which means that ```s``` is greater than the sum of ```arr```. Therefore the smallest ```s``` possible is ```sum(arr) + 1```.

For guess #2, we can prove it using the result from guess #1.

First, we rewrite guess #2 into a more mathematical term: Given the current array is "non-sparse" and the choice to add ```s = sum(arr) + 1``` or an arbitrary ```p < s``` to the array, adding ```s``` is always the better choice, i.e. create an array that could "sum" more numbers.

By the result from guess #1, we know that for all ```x < s```, ```x``` is sumable without adding any additional value. From there we could argue that by adding either ```s``` or ```p``` to ```arr```, the new array, ```arr_new```, is also "non-sparse". This is because either ```s``` is added, ```max(arr_new) = s``` which is sumable, and therefore all numbers smaller or equal to ```max(arr_new)``` is sumable, or ```p``` is added, ```max(arr_new) = max(p, max(arr)) < s```, and by definition all numbers smaller or equal to ```max(arr_new)``` is sumable.

Now we use the result from guess #1 again which gives us the total sumable numbers of ```arr_new``` for adding ```p``` is ```sum(arr) + p < sum(arr) + s```, the total sumable numbers for adding ```s```. This means adding any ```p < s``` to ```arr``` would lead to less number being able to be sumed from ```arr_new```, compared to adding ```s``` to ```arr```. Therefore adding ```s``` to ```arr``` is always the better choice.

Now that we've proved these assumptions, its easy to write a code to solve this problem in ```O(log(n))```: Start with an empty list, ```arr```, and a counter, ```result```, calculate the current ```s = sum(arr) + 1```. If the first value in ```arr``` smaller or equal to ```s```, remove it from ```nums``` and add it to ```arr```. If the first value is larger than ```s```, add ```s``` to ```arr``` and add 1 to ```result```. Now recalculate ```s``` and repeat this process again until ```s``` > ```n```. Return the counter, ```result```.
