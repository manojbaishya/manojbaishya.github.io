Recently, I came across a very interesting problem in an online forum that deals with objects having a natural order (think `comparable` types in Go or C#'s `IComparable<T>`, etc)

I had the opportunity to brainstorm a solution and am enthusiastic to share it with the community.

Imagine having two streams, containing such comparable objects originating from two sources. In the scenario, the sources are homogeneous but distributed and there is need to compute summary statistics from the aggregate of the two. 

While there are the multiple important metrics that can give insight into the data, one such important metric is the median, which divides the population of sample points into symmetrical halves.

This kind of "sub-problem", lets's assume, frequently arises in the context of sensor networks, search engines, sequence alignment in bioinformatics, etc.

The question is framed as follows: given two sorted lists S and B of sizes N1 and N2, the problem is to efficiently find the median of the combined list without explicitly merging them.

One thing we can do is merge the two lists, sort them and then compute the median from the middle indices. But each step in that pipeline does a lot of work - in total O(N1 + N2) + O((N1 + N2) x log2(N1 + N2))!

Can we think a little deeply, may be, creatively use the mathematical properties of 'sorted collections' and avoid doing the expensive intermediate work? Let's find that out.

First let's assume the two lists are merged into a ghost array, M. Then we will map the observations from M onto our two separate sorted arrays S and B and find one-to-one correspondences between the two layouts.

In M, the median divides the population into a symmetry of equal populations: the left half has all elements lesser than that of the right half. 

Assume M is written out on a single line in a sorted order, and then draw a vertical "divider" line between the two populations, so as to partition them.

Now we are going to rearrange the line into two lines stacked on top of each other while keeping the divider line intact, such that each of those lines represent S and B, with S in top and B in bottom. While doing this, keep in mind to maintain the sorted order of S and B respectively. Let's call this picture, the 'lane layout of partitioning of M'.

This rearranged drawing illustrates S and B's contributions to the left partition and the right partition of the ghost merged array M.

The facts are:
    1. Each element on the left partition is lesser than all of those in the right partition.
    2. Count of total elements on the left partition is equal to that in the right partition due to the median, and it is constant, say L.

Now our questions are:
1. How many and which contributions from S went into the left and right partitions of the ghost array M?
2. How many and which contributions from B went into the left and right partitions of the ghost array M?

Let's try to use the facts to arrive at the answers to the above questions.

We know the array sizes N1 and N2, and that the partition sizes are constant and equal, L.

If S contributed 'SLCount' items to the left partition of M, then S's contribution to the right partition is N1 - SLCount.
Then, since left partition size is L, B will have to contribute "L - SLCount" items to the left partition to maintain it's size invariant. Also, then B will contribute "N2 - (L - SLCount)" items to the right partition of M.

So! Just by playing detective with the number "SLCount", we can derive the other numbers of questions 1 and 2 and have indirectly arrived at the partitioning.

Computing the median, once we have the correct partitions, is easy. If M is evenly sized, take the max from the left partition and the min from the right partition, and average the two. If M has odd count, and assuming in the rearrangement of M into S and B, we kept the higher contribution counts of each on the left partition, then median is simply the max of the left partition.

Therefore, the game is find out how many elements S contributed in the left partition of M, and the rest of the observations follow from the facts.

We can exploit another property of sorting in this game. If we were to do trial-and-error starting from 0 counts of S to whole of S to determine SLCount, we are looking at a linear time computation.
Instead, since S is sorted, possibly we can use binary search to determine the correct SLCount?
Let's bound the SLCount into a range, lowSLCount = 0 and HighSLCount = size of S, which is N1. We are going to narrow down to a number between 0 and N1 using binary search.
For starters, let's take the average of lowSLCount and HighSLCount as testSLCount. The search space for testSLCount will narrow down as we test exit conditions.
How would we know which SLCount is correct? For that we need to go back to our partitioning picture of M, and derive the exit conditions.

In the 'lane layout of partitioning of M', we know that all elements of S lane on the left partition are smaller that all elements of S lane on the right partition. Similarly, all elements of B lane on the left partition are smaller that all elements of B lane on the right partition.
Therefore the intra-lane ordering is intact.

However, if we prove cross-lane ordering across the divider line from S and B, then we will have successfully proven that the left and right partitions are correct, therefore SLCount is the correct number.

There's an easy way to do so:
Find the max of S's contributions in the left partition and test if it is less than the min of B's contributions in the right partition.
And, the max of B's contributions in the left partition and test if it is less than the min of S's contributions in the right partition.
If for a particular SLCount, if the above two tests pass, then we have found the correct partitioning and hence, SLCount!

What if either of the tests fail? 
If max(S in Left) > min(B in right), then we must take lesser contributions from S and more contributions from B. I call this the clockwise rotation of 'lane layout of partitioning of M'. Reduce the search space to lowSLCount = 0 and HighSLCount = testSLCount - 1, since testSLCount must be lesser than its current value.
If max(B in Left) > min(S in right), then we must take higher contributions from S and reduce contributions from B. I call this the counter-clockwise rotation of 'lane layout of partitioning of M'. Reduce the search space to lowSLCount = testSLCount + 1 and HighSLCount = N1, since testSLCount must be higher than its current value.

Eventually, this process is guaranteed to converge to the correct SLCount, since a partitioning of M always exists!
The binary search on the smaller list ensures that the partitioning check runs in O(log(min(N1,N2))) time complexity, reducing overhead compared to direct merging."

If you are with me so far, thanks for reading!