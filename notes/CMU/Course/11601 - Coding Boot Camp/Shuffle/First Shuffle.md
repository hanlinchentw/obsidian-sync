Interviewee: RuiChen
Date: Sep 6, 2026 2 p.m
Question: 27.8, 27.2, 26.2

### 27.8

### 27.2 Smaller prefixes
Given an array of integers, arr, where the length, n, is even, return whether the following condition holds for every k in the range 
`1 <= k <= n/2`: "the sum of the first k elements is smaller than the sum of the first 2k elements." if this condition is false for any k in the range, return false.

> Example: arr = [1, 2, 2, -1]
> Output: True. The prefix [1] has a smaller sum than the prefix [1, 2], and the prefix [1, 2] has a smaller sum than the prefix [1, 2, 2, -1]. The other prefixes have length > n/2

> Example: arr = [1, 2, -2, 1, 3, 5]
> Output: False. The prefix [1, 2] has a larger sum than the prefix [1, 2, -2, 1].


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