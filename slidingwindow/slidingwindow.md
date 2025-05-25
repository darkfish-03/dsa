## Sliding Window

### Constant Window

Ex : Find subarray of size k with largest sum in a given array. 

Compute the sum (processing) for the first window with l=0 and r=k-1;
then, 
```
while(r < n) {
   sum = sum - arr[l];
   sum += sum + arr[r];
   l++; r++;
   maxSum = max(maxSum, sum);
}
return maxSum;
```

### Longest subarray/ substring where <condition>

Ex : Longest subarray with sum <= k

Better Solution 
Expand r and Shrink l. 
At any moment length of window is r - l + 1.

```
l = 0;
r = 0;
sum = 0;
maxLen = 0;

while(r<n) {
  sum += arr[r]; // conditional
  while(sum > k) { // conditional
    sum -= arr[l]; // conditional
    l++;
  }
  if (sum <= k) { // conditional
    maxLen = max(maxLen, r-l+1); 
  }
  r++;
}
return maxLen;

TC : O(2N)
SC : O(1)
```

Optimal Solution 

If ans is just the length then we do not need to shrink beyond max length reached so far. 

```
l = 0;
r = 0;
sum = 0;
maxLen = 0;

while(r<n) {
  sum += arr[r]; // conditional
  if(sum > k) { // conditional
    sum -= arr[l]; // conditional
    l++;
  }
  if (sum <= k) { // conditional
    maxLen = max(maxLen, r-l+1); 
  }
  r++;
}
return maxLen;

TC : O(N)
SC : O(1)
```

### Number of subarray/ substring where <condition>

Ex : Number of subarray with sum = k 

Find number of subarray with sum <=k [x] and sum <= k-1 [y] ans : x - y

### Shortest/ Minimum Window where <condition>




