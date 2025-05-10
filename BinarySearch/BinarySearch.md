# Binary Search


## Iterative 
```
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] == target) {
      return mid;
    }
    if(target > a[mid]) {
      low = mid+1;
    } else {
      high = mid-1;
    }
  }
  return -1;
}
```

## Recursive

```
int binarySearch(vector<int> a, int target, int low, int high) {
  if(low > high) {
    return -1;
  }
  int mid = low + (high - low)/2;
  if(a[mid] == target) {
      return mid;
  } 
  if(target > a[mid]) {
      return binarySearch(a, target, mid+1, high);
  } else {
      return binarySearch(a, target, low, mid-1);
  }
}
```
