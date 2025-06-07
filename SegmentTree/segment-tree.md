```

segment[4*N];


void build(int index, int low, int high) {
    if (low == high) {
        segment[index] = arr[low];
        return;
    }
    int mid = low + high / 2;
    build(2*index+1, low, mid);
    build(2*index+2, mid+1, high);
    segment[index] = min(segment[2*index+1], segment[2*index+2]);
}


int query(int index, int low, int high, int l, int r) {
    if (l >= low && r<= high) { // complete overlap
        return segment[index];
    }
    
    if (high < l || low < r) { // completely outside
        return INF;
    }
    int mid = low + high / 2; // partial overlap
    int left = query(2*index+1, low, mid, l, r);
    int right = query(2*index+2, mid+1, high, l, r);
    return min(left, right);
}


```
