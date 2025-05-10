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

## Lower Bound and Upper Bound

1. Lower Bound = smallest index such that arr[ind] >= target
2. Upper Bound = smallest index such that arr[ind] > target
3. Search insert position, ceil is similar to finding lower bound

```
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = a.size();
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] >= target) {
      ans = mid;
      high = mid - 1;
    } else {
      low = mid + 1;
  }
  return ans;
}
```

## Floor in an array

```
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = -1;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] <= target) {
      ans = a[mid];
      low = mid +1;
    } else {
      high = mid - 1;
  }
  return ans;
}
```

## First Occurrance and Last Occurance in an array

1. First Occurrance  == lowerBound
2. Last Occurance = upperBound -1 if lowerBound exists

```
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = -1;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] == target) {
      ans = a[mid];
      high = mid - 1;
    }
    if (a[mid] > target) {
      high = mid - 1;
    } else {
      low = mid + 1;
    }
  }
  return ans;
}
```


## Search in a rotated array


```
// unique elements 
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = -1;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] == target) {
      return mid;
    }
    if (a[low] <= a[mid]) { // left sorted
      if (target >= a[low] && target <= a[mid]) {
          high = mid - 1;
      } else {
          low = mid + 1;
      }
    } else { // right sorted
      if (target >= a[mid] && target <= a[high]) {
          low = mid + 1;
      } else {
          high = mid - 1;
      }
    }
  }
  return -1;
}

// duplicate elements
[3, 1, 2, 3, 3, 3, 3] : you can't tell which part is sorted
 this can happen if arr[low] == arr[high] == arr[mid] --> trim the search space

int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = -1;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if(a[mid] == target) {
      return mid;
    }
    if (arr[low] == arr[mid] && arr[high] == arr[mid]) {
        low++;
        high--;
        continue;
    }
    if (a[low] <= a[mid]) { // left sorted
      if (target >= a[low] && target <= a[mid]) {
          high = mid - 1;
      } else {
          low = mid + 1;
      }
    } else { // right sorted
      if (target >= a[mid] && target <= a[high]) {
          low = mid + 1;
      } else {
          high = mid - 1;
      }
    }
  }
  return -1;
}


```

## Find minimum in a rotated array


```
// unique elements 
int binarySearch(vector<int> a, int target) {
  int low = 0;
  int high = a.size()-1;
  int ans = INT_MAX;
  while(low <= high) {
    int mid = low + (high - low)/2;
    if (a[low] <= a[mid]) { // left sorted
      ans = min(ans, a[low]);
      low = mid + 1;
    } else { // right sorted
      ans = min(ans, a[high]);
      high = mid - 1;
    }
  }
  return ans;
}
```

