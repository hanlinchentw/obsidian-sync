
You're given a string schedule, like "011001":

- '1' = working day

- '0' = rest day

You can flip at most k rest days (0s) into working days (1s).

Each working day gives fixed pay.

Bonus pay is given only if you work consecutively, starting from the second consecutive day (i.e., streaks give bonus from the second day onward).

Example: "011" → Day 2 and Day 3 are working

Day 2: fixed pay only

Day 3: fixed + bonus pay

> I used backtrace technique. hit time limit on some test cases.


Sliding window is the optimal solution
```
int maxPay(string schedule, int k, int fixed, int bonus) {
	int n = schedule.size();
	vector<int> work;
	for (int i = 0; i < n; i++) work.push_back(schedule[i] - '0');
	int left = 0, zeroCount = 0, maxLen = 0, bestL = 0, bestR = 0;
	for (int right = 0; right < n; right++) {
		if (work[right] == 0) {
			zeroCount++;
			
		}
		while (zeroCount > k) {
			if (work[left] == 0) zeroCount--;
			left++;	
		}
		if (right - left + 1 > maxLen) {
			maxLen = right - left + 1;
			bestL = left;
			bestR = right;
		}
	}

	int flips = k;
	for (int i = bestL; i <= bestR; i++) {
		if (work[i] == 0 && flips > 0) {
			work[i] = 1;
			flips--;
		}
	}

	int pay = 0;
	for (int i = 0; i < n; i++) {
		if (work[i] == 1)
			pay += fixed;
		if (i > 0 && work[i] == work[i-1])
			pay += bonus;
	}
	return pay;
}
```