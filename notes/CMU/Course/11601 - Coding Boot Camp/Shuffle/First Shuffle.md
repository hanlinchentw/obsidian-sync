Interviewee: RuiChen
Date: Sep 6, 2026 2 p.m
Question: 27.8, 27.2, 26.2

### 27.8 3-way merge without duplicates
Given three sorted arrays, arr1, arr2, and arr3, return a new array with the elements of all three arrays, in sorted order, without duplicates.

> Example: arr1 = [2, 3, 3, 4, 5, 7], arr2 = [3, 3, 9], arr3 = [3, 3, 9]
> Output: [2, 3, 4, 5, 7, 9]



### 27.2 Smaller prefixes
Given an array of integers, arr, where the length, n, is even, return whether the following condition holds for every k in the range 
`1 <= k <= n/2`: "the sum of the first k elements is smaller than the sum of the first 2k elements." if this condition is false for any k in the range, return false.

> Example: arr = [1, 2, 2, -1]
> Output: True. The prefix [1] has a smaller sum than the prefix [1, 2], and the prefix [1, 2] has a smaller sum than the prefix [1, 2, 2, -1]. The other prefixes have length > n/2

> Example: arr = [1, 2, -2, 1, 3, 5]
> Output: False. The prefix [1, 2] has a larger sum than the prefix [1, 2, -2, 1].

Constraint:
arr length >= 2

Possible Solution: Two pointers, prefix sum

Hint 1:
Can you track two different sums at the same time, each representing the sum of different prefix lengths (k and 2k)?

Hint 2: 
Consider two pointers or indices moving with different steps
Think of maintaining two running sums simultaneously—one for the prefix length k, one for prefix length 2k.

Two pointers:
```python
def smaller_prefixes(arr):
  if not arr:
    return False
  slow, fast = 0, 0
  slow_sum, fast_sum = 0, 0
  while fast < len(arr):
    slow_sum += arr[slow]
    fast_sum += arr[fast] + arr[fast+1]
    if slow_sum >= fast_sum:
      return False
    slow_sum += 1
    fast_sum += 2
  return True
```

Prefix:
```python
def smaller_prefixes(arr):
    if not arr:
        return False
    n = len(arr)
    prefix_sum = [0] * (n+1)
    for i in range(1, n):
        prefix_sum[i] = prefix_sum[i-1] + arr[i]

    for k in range(1, n // 2):
        if prefix_sum[k] >= prefix_sum[2*k]:
            return False
    return True
```

### 26.2
Without using a built-in string join method, implement a join(arr, s) method, which receives an array of strings, arr, and a string s, and returns a single consisting of the strings in arr with s in between them.
> Example 1
> arr = ["join", "by", "space"], s = " "
> Output: "join by space"

Example 2
> arr = ["b", "", "k", "p", "r n", "", "d", "d!!"], s = "ee"
> Output: "beeeekeepeer neeeedeed!!"

Since python string is immutable, assume that you have access to a function array_to_string(arr), which takes an array of individual characters and returns a string with those characters in O(len(arr)) time.

Solution:
```python
def join(arr, s):
  res = []
  for i in range(len(arr)):
    if i != 0:
	  for c in s:
        res.append(c)
    for c in arr[i]:
      res.append(c)
  return array_to_string(res)
```