Heap supports Insert() in O(logN), Search() in O(N), FindMin() in O(1) and Delete() in O(logN) time complexity. 

----
In a tree where all levels are completely filled is called perfect binary tree. 
In an alomost complete binary tree, leaves should be present only at the last 2 levels. And
leaves at same level should be filled from left to right. 
A complete binary tree is a binary tree where all levels are filled except possibly the last level. 
In a complete binary tree, the nodes in the last level are filled from left to right. 

----

Heap is a tree based datastructure. 
Heap is a complete tree. 
This data structure must follow heap property. 
Types - MinHeap, MaxHeap. 

MaxHeap : It is a complete binary tree, in which root should always be maximum and same goes for all sub-trees. 
MaxHeap : It is a complete binary tree, in which root should always be minimum and same goes for all sub-trees. 



Heap Representation 

A tree can be represented in form of an array. 
In a zero based indexing, if parent is at index i, then
leftChild = 2i+1;
rightChild = 2i+2;
parent = ceil(i/2)-1;

How large the array should be ?

If h is the height of tree, then number of nodes only at height h = 2^h;
And maxiumum number of nodes in the tree = 2^(h+1) - 1;
In a perfect binary tree number of internal node = number of leaves -1;

Height of a tree = floor(logN);

In a complete binary tree, 
for 1 based indexing (otherwise subtract 1)
range of internal nodes = 1 to floor(N/2);
range of leaves nodes = floor(N/2)+1 to N;

------------------

Heapify Algorithm / Percolate Down

A leaf node always follows the heap property since we have no left/ right subtree. 

The process of rearranging the heap by comparing each parent with its children recursively is known as heapify. 

This process can be applied if left and right subtree is already heapified for a given node. 

```
void max_heapify (int Arr[ ], int i, int N)
{
    int left = 2*i                   //left child
    int right = 2*i +1           //right child
    if(left<= N and Arr[left] > Arr[i] )
          largest = left;
    else
         largest = i;
    if(right <= N and Arr[right] > Arr[largest] )
        largest = right;
    if(largest != i )
    {
        swap (Ar[i] , Arr[largest]);
        max_heapify (Arr, largest,N);
    } 
 }

TC : O(logN)
SC : O(logN)
```

------------------
Build Heap Algorithm

```

void build_maxheap (int Arr[ ]) 
{
    for( int i = N/2 ; i >= 1 ; i--)
    max_heapify (Arr, i);
}

TC : O(N). max_heapify function has complexity log N and the build_maxheap functions runs only N/2 times,
but the amortized complexity for this function is actually linear.
SC : O(logN)
```

------------------

Extract Max

a. Save the max value.
b. Swap the last to root
c. Remove last i.e decrease heapsize
d. Heapify root

```
int extract_maximum (int Arr[ ])
{
    if(length == 0)
    {
cout<< “Can’t remove element as queue is empty”;
        return -1;
    }
    int max = Arr[1];
    Arr[1] = Arr[length];
    length = length -1;
    max_heapify(Arr, 1); // on root
    return max;
}
```

Increase Key 

On increasing the value of a node in a maxHeap, a node may move up to maintain the maxHeap property. 

a. Update the node value
b. percolate up till - parent > currNode or currNode becomes root. 

```
void increase_key(int Arr[ ], int i, int key) {
    if (key < Arr[i]) {
        cout<<”New value is less than current value, can’t be inserted” <<endl;
        return;
    }
    Arr[i] = key;
    while(i > 0 && Arr[i] > Arr[i/2]) {
        swap(Arr[i], Arr[i/2]);
        i = i/2;
    }
}

TC : O(logN)
SC : O(1)
```

Increase Key 

On decreasing the value of a node in a maxHeap, a node may move down to maintain the maxHeap property. 

a. Update the node value
b. percolate down

```
void increase_key(int Arr[ ], int i, int key) {
    if (key > Arr[i]) {
        cout<<”New value is greater than current value, can’t be inserted” <<endl;
        return;
    }
    Arr[i] = key;
    max_heapify(Arr, i)
}

TC : O(logN)
SC : O(logN)
```

Insert Key 

a. Update the node value
b. percolate up

```
void insert_value (int Arr[ ], int val)
{
    length = length + 1;
    Arr[ length ] = -1;  //assuming all the numbers greater than 0 are to be inserted in queue.
    increase_val (Arr, length, val);
}
```



