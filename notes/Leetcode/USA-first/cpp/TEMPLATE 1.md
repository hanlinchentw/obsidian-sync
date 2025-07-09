### Thinking：
1. loop through the grid
	1. when finding 1, number of island++;
	2. use dfs traverse every node in that island
	3. mark 0 whenever we pass a node

### Solution:

```
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int ans = 0;
        for (int i = 0; i < grid.size(); i++) {
	        for (int j = 0; j < grid[0].size(); j++) {
		        if (grid[i][j] == '1') {
			        ans++;
			        goThrough(grid, i, j);
		        }
	        }
        }
        return ans;
    }

	void goThrough(vector<vector<char>>& grid, int i, int j) {
		if (i < 0 || i >= grid.size() || j < 0 || j >= grid[0].size() || grid[i][j] == '0')
			return;
		grid[i][j] = '0';
		goThrough(grid, i+1, j);
		goThrough(grid, i-1, j);
		goThrough(grid, i, j+1);
		goThrough(grid, i, j-1);
	}
};
```

review: