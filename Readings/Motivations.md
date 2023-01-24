
# Predicate Invention

Introduction...



## Introduce Counting system

---

### Problem 01: Group-X-Y

Given X and Y as two positive integers, we can generate a set of images following the rules: 
- Each image has X groups of objects which distinguish with some kind of property 
(like color, shape, etc). 
- Each group has exactly Y objects.

### Idea

Let's start from X=2, Y=3, i.e. 2 groups, 3 objects in each group:

We need a counter to count the number of objects.
Introduce new neural predicate:
```
one(A, B, p) // from position A to B, there is one object with property p
zero(A, B, p) // from position A to B, there is no object with property p
```
these two predicates are supposed as neural predicate, 
thus the perception model should be able to provide a confidence to each predicate.

Then, the Predicate Invention **(PI)** model is supposed to do the following jobs to count 1st group
```
inv_1(A, B, p1) :- one(A, C, p1), one(C, B, p1) 
inv_2(A, B, p1) :- inv_1(A, C, p1), one(C, B, p1)
```
where `inv_1` and  `inv_2` are invented predicates. 
`ìnv_1` means from A to B there are 2 objects with property `p1`.
`ìnv_2` means from A to B there are 3 objects with property `p1`.
Until here, the PI already give the ability to count to 3 objects in one group. 
For a positive image, the argument A should be the first pixel/search_grid of the image, 
the argument B should be the last pixel/search_grid. 
Then, we can say, the image has exact 3 objects with this property.

For the case of 2nd group, the clause should be found as follows
```
inv_1(A, B, p2) :- one(A, C, p2), one(C, B, p2)
inv_2(A, B, p2) :- inv_1(A, C, p2), one(C, B, p2)
 ```
in this case, no new predicate is invented when count the group with property `p2`, which is an ideal situation 
so the invented predicates `inv_1, inv_2` can be reused.

So far, the useful conclusions are `inv_2(A, B, p1)` and `inv_2(A, B, p2)`, 
which count groups with property `p1` and `p2` to 3, respectively. 

At last, one more predicate is required `diff_prop(p1, p2)`, 
which can be designed as background knowledge. So the final clause can be 
``` 
positive(img) :- inv_2(A, B, p1), inv_2(A, B, p2), diff_prop(p1, p2)
```

##### Discussion

In this example, two new predicates have been invented, `inv_1(A,B,p)` and `inv_2(A,B,p)`,
which count the object with property `p` to 2 and 3, respectively. 
Where `A` and `B` in the predicates denote for the positions in the images. 

1. `zero(A,B,p)`: It is not always the case that `B` are the same. So we have to use some clause to extend B  to the last pixel/search_grid, 
so that we can guarantee there are always two counters corresponding the whole images. 
We can use a `zero(A,B,p)` predicate do the trick 
```
inv_2(A, B, p) :- inv_2(A, C, p), zero(C, B, p) 
```
2. `one(A,B,p)`: By adding this predicate, the ILP system is supposed to count any number of objects from position
`A` to `B` with the same property `p`, which can use the clause
```
inv_1(A,B,p) :- one(A,C,p), one(C,D,p), ..., one(X,B,p)
```
where `inv_1(A,B,p)` is an invented predicate.





### TODO List
- Generate positive and negative images.
- 