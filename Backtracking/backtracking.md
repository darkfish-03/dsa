### Backtracking 

##### Difference b/w Recursion / Backtracking/ DP ?

Recursion = choices + decision [ans is usually on leaf nodes]
DP = optimal [answer lies on root node] Recursion + Memory
Backtracking = Recursion + Control [combinations] Answer lies on path

Backtracking = Controlled Recursion + Pass By Reference

##### Identification

Choices + Descision
"All" Combination
Controlled Recursion
Large Number of choices (can be variable) 
Less Size of constraint
Dont be greedy (largest number in k swaps 1234567, 4577 swap first will largest and so on will fail) think of counter example

##### Generalisation

```
void solve(var) { // pass by ref
  if (isSolved() == True) {
    print// save
  }

  for (c in all choices) {
    if (isValid() == True) { // control
      changes in var (v + c1)
      solve(var)
      revert changes in var (v - c1)
    }
  }
}
```

##### Problems
Permutation of a string

largest number in k swaps

n digit number with digits in increasing order

rat in a maze

palindrome partitioning

combination sum

suduko

n queens

word break

letter combinatiions of a phone number

