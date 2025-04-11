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






