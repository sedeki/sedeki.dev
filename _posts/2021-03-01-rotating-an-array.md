---
title: How to Rotate an Array
date: 2021-03-02 08:00:00
---

I thought I would know how to solve a problem, filed under "easy", at LeetCode.
The problem was to *rotate* an array $$A$$ by $$k$$ elements, and
furthermore, doing it with $$O(1)$$ memory usage.

To be fair, this additional memory specification was not *required* per se in
the original problem statement, and having it was explicitly stated as being a harder problem to tackle.
But it made the problem much more interesting to me, and was more complex than I originally thought.

I am writing this blog post in order to reflect on what I have learnt and my thought process.

## Problem Statement

We begin with the problem formulation.

To *rotate* an array by $$k = 1$$ element means specifically that

1. Pop the last element $$x$$ from the array $$A$$.
2. Prepend $$x$$ to $$A$$.

Concretely, let us assume that $$A = [1, 2, 3, 4, 5]$$ and $$k = 1$$.
Then $$r_1(A) = [5, 1, 2, 3, 4]$$ where $$r_k$$ is the rotate-by-$$k$$ function.

If $$k > 1$$, then just apply $$r_1$$ a total of $$k$$ times:
$$r_k(A) = \underbrace{r_1 \circ \cdots \circ r_1}_{\text{$k$ times}}(A)$$.

(Obviously, if $$k = 0$$ then we return the input array as-is.)

The problem statement is thus:

> Given an array $$A$$ and an integer $$k \geq 0$$, what is the resulting array
> from rotating $$A$$ by $$k$$?

## The Naive Solution

Without the $$O(1)$$ memory constraint, this is an easy problem. Simply write

```python
def rotate(A, k):
    k = k % len(A)
    merged = list(A) + list(A)
    return merged[len(A) - k:2*len(A) - k]
```

For example, `rotate([1, 2, 3, 4, 5], k=2)` will do the following:
Merge the input array with a copy of itself and return an appropriate subset of that array.
I am also required to add the line `k = k % len(A)` to this solution, but it should be
fairly obvious why that is.

The following image illustrates the merged array, called `merged` above, and the highlighted
part illustrates the subarray that we return:

![](/assets/rotate-naive.png)

Of course, if the array is big, this is not an optimal solution, because we'd
have to construct an huge intermediate array before we can return what we actually want.
Specifically, it allocates double the amount of memory regardless of what is passed in as input.
Not good.

## Circular Dependencies

How can we do better? It might be tempting to write a function like the one below, but it
is plain wrong, because it will overwrite values that we'll need in a later iteration.

```c++
// Not a solution
void rotate(std::vector<int> &nums, int k) {
    for (int i = 0; i < nums.size(); ++i) {
        int index = (i + k) % nums.size();
        std::swap(nums[i], nums[index]);
    }
}
```

To dig a bit deeper on this point, let us consider $$A = [1, 2, 3, 4, 5]$$ and $$k = 2$$ again.
Consider instead the new array that we *want* to return, and how it relates to the original array.

Let us manually trace the assignments from the original array to itself, in order for us to build
an understanding of which order to do things in. We do this by going back and forth between the
indices of the original and the "new" array. Hopefully this will lead to a solution.

The value of the first element (as seen above in the illustration), is 4.
This corresponds to the index 3 in the old array, which in the wanted array has value 2.
And at index 1 in the wanted array is, in turn, the value 5. And at index 4, we have the value 3.
Finally, at index 2 we have the value 1, and we are back to where we started.

What we have just done can be summarized, in code, by the following assignments:

```python
A = [1, 2, 3, 4, 5]
A[0] = A[3]
A[3] = A[1]
A[1] = A[4]
A[4] = A[2]
A[2] = A[0]
# A = [4, 5, 4, 2, 3]
```

This yields the correct result except for the element at index 2, which is our last assignment.
The value there is 4, which is a duplicate value in the array as it already exists at index 0.
Unfortuantely, we cannot just reorder the assignments in order to get the correct result, as we
end up in a kind of cycle, or *cyclic dependency*.

Obviously, the pairs of indices in the above assignments differ by 2 modulo 5.
This is fairly obvious since we are shifting the indices by $$k = 2$$.

$$
\begin{align*}
0 - 3 \equiv 2 \mod 5 \\
3 - 1 \equiv 2 \mod 5 \\
1 - 4 \equiv 2 \mod 5 \\
4 - 2 \equiv 2 \mod 5 \\
2 - 0 \equiv 2 \mod 5 \\
\end{align*}
$$

The above assignments can be compactly written as $$[0, 3, 1, 4, 2]$$.
Here, the order is important.

Note that there can be more cycles than one. It is easily seen with a bit of
experimentation.
For example, these are the cycles with $$A = [1, \ldots, 12]$$ and $$k = 3$$:

$$
\{[0, 3, 6, 9], [1, 4, 7, 10], [2, 5, 8, 11]\}
$$

Let $$n$$ be the length of $$A$$. The number of cycles is $$\gcd(n, k)$$,
and each cycle is of length $$n/\gcd(n, k)$$. Furthermore, two consecutive indices
within each cycle differ by $$k$$ modulo $$n$$ as above, and if $$\gcd(n, k) > 1$$,
then each of the indices $$\{0, \ldots, \gcd(n, k) - 1\}$$ belong to separate cycles.

## Solution

```c++
int gcd(int a, int b) {
    while (b != 0) {
        int t = b;
        b = a % b;
        a = t;
    }
    return a;
}

void rotate(std::vector<int> &nums, int k) {
    if (nums.empty()) {
        return;
    }
    k = k % nums.size();
    if (k == 0) {
        return;
    }
    int num_cycles = gcd(nums.size(), k);
    for (int i = 0; i < num_cycles; ++i) {
        int j = (i - k + nums.size()) % nums.size();
        int temp = nums[j];
        while (j != i) {
            int next = (j - k + nums.size()) % nums.size();
            nums[j] = nums[next];
            j = next;
        }
        nums[i] = temp;
    }
}
```
