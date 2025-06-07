## Build & Query

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


## Point Update 

```

void update(int index, int low, int high, int node, int val) {
    if (low == high) {
        segment[low] = val;
        return;
    }
    int mid = low + high / 2;
    if (node>=low && node <= mid) {
        update(2*index+1, low, mid, node, val);
    } else {
        update(2*index+2, mid+1, high, node, val)
    }
    segment[index] = min(segment[2*index+1], segment[2*index+2]);
}

```

## Lazy Propogation

```

void rangeUpdate(int index, int low, int high, int l, int r, int val) {

    if (lazy[index] != 0) {
        segment[index] = (high - low + 1)*lazy[index];
        if(low != high) {
            lazy[2*index+1] += lazy[index];
            lazy[2*index+2] += lazy[index];
        }
        lazy[index] = 0;
    }
    if (l >= low && r<= high) { // complete overlap
        segment[index] = (high - low + 1)*val;
        if(low != high) {
            lazy[2*index+1] += lazy[index];
            lazy[2*index+2] += lazy[index];
        }
        return;
    }
    
    if (high < l || low < r) { // completely outside
        return;
    }
    int mid = low + high / 2; // partial overlap
    rangeUpdate(2*index+1, low, mid, l, r, val);
    rangeUpdate(2*index+2, mid+1, high, l, r, val);
    segment[index] = min(segment[2*index+1], segment[2*index+2]);
}

int query(int index, int low, int high, int l, int r) {

    if (lazy[index] != 0) {
        segment[index] = (high - low + 1)*lazy[index];
        if(low != high) {
            lazy[2*index+1] += lazy[index];
            lazy[2*index+2] += lazy[index];
        }
        lazy[index] = 0;
    }
    if (l >= low && r<= high) { // complete overlap
        return segment[index];
    }
    
    if (high < l || low < r) { // completely outside
        return 0;
    }
    int mid = low + high / 2; // partial overlap
    int left = query(2*index+1, low, mid, l, r);
    int right = query(2*index+2, mid+1, high, l, r);
    return min(left, right);
}

```
