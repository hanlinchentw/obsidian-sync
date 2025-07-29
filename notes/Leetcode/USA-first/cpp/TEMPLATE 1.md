### Thinking：

### Solution:

```
class Solution {
public:
    int stoneGameII(vector<int>& piles) {
	    int maxi = 0;
	    dfs(0, piles, 1, 0, maxi, true);
	    return maxi;
    }

    void dfs(int start, const vector<int>& piles, int M, int curr, int& maxi, bool aliceTurn) {
	    if (start >= piles.size()) {
		    maxi = max(maxi, curr);
		    return;
	    }
		for (int X = 1; X <= 2 * M; X++) {
			if (aliceTurn) {
				int accu = 0;
				for (int i = start; i < start + X && i < piles.size(); i++)
					accu += piles[i];
				dfs(start+X, piles, max(M, X), curr+accu, maxi, false);
			} else {
				dfs(start+X, piles, max(M, X), curr, maxi, true);
			}
		}
    }
};
```

review: