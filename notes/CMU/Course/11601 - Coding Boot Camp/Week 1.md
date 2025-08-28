### Computational complexity
O(1), O(logn), O(n), O(n^2), O(n^3), O(2^n)
Search in a linked list: O(n)
Search in a binary tree: O(2^n)
Search in a BST: best-balanced O(logn), worst-unbalanced O(n)

### Regular Expressions
character literal
- 'a' matches the letter 'a'
- wildcard, '.' matches any character, newlines typically included if multi-line strings supported
character set
- [aeiou] matches any vowels
- [A-Z] matches any Upper cases
repetition
- X?: any appearances
- X*: more
- X+: 1
\t tab
\n newline
\r carriage-return
\s
\S
\d
\D any decimal
\w any word character - [A-Za-z0-9]
\W any nonword character - [^A-Za-z0-9]