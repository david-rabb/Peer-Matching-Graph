# Peer Matching Graph Algorithms
Algorithms to solve the graph problems of matching students with their peers to evaluate group projects.

## Introduction
In a school classroom, the instructor decides some coursework should be completed in groups. When the groups submit their assignment (whether written, oral, performance, etc.) other students will evaluate their submission. This gives students new depths of understanding the course material as they evaluate other's work and also the chance to receive valuable peer feedback. 

Given arbitrary classroom sizes, group sizes and number of evaluations expected, it becomes an algorithmic challenge to evenly distribute peer evaluators across the assignments they are evaluating. How do we guarantee each student submits the same number of evaluations? How do we guarantee each group receives the same number of evaluations? And how do we make sure nobody evaluates himself? 

If the instructor randomly assigns groups to students for evaluation (say by letting students pick group names out of a hat), it's likely to fail because a student has picked his own group. Each student has a small chance of picking his own group, but somewhat counter-intuitively (as in the Birthday Paradox), the chance of it happening once in the classroom can be significant when the individual probabilities are combined.

## Analysis

### Assumptions & Goals

* The problem assumes a classroom of at least two students and at least two groups where each student belongs to one group and must evaluate one or more other groups.
* The instructor could decide to specify the number of evaluations each student is expected to give or specify the number of evaluations each group is expected to receive, but not both. In this solution we will focus on the former (choosing a number of evaluations each student should submit), but the solution could be easily transposed to solve for the other scenario.
* Given varying group sizes, specifying one aspect above will imply an _average_ size of the opposing aspect. The final algorithm is designed to minimize variance within some tolerance, but this is constrained by input parameters. For example, if we have a classroom of ten students and nine of them are in one group, this will grossly limit our possible solutions.
* There is no single ideal arrangement at this stage -- the instructor should be able re-run the algorithm and see different combinations. In the future, some weighting preferences may be applied based on external data. For example:
  * It may be preferable that students evaluate different groups for subsequent group coursework.
  * It may be preferable to pair some students together as an experimental control.
* Algorithm performance is not a major concern considering that classroom sizes are typically < 100 students. However, the algorithm should attempt quadratic (_n_<sup>2</sup>) or better performance. 

### Graph Theory
We can describe the classroom as a biparite graph _G = (E,V)_ with vertices for students _V_<sub>s</sub> and groups _V_<sub>g</sub>. Connecting the two types of vertices are two types of edges representing group membership _E_<sub>m</sub> and peer evaluators for each group _E_<sub>n</sub>. The total number of vertices is |_V_<sub>s</sub>| + |_V_<sub>g</sub>| where |_V_<sub>g</sub>| &le; |_V_<sub>s</sub>|. The total number of edges is |_E_<sub>m</sub>| + |_E_<sub>n</sub>| where |_E_<sub>n</sub>| = n |_V_<sub>s</sub>| (number of evaluations per student x number of students). 

To setup the problem, each student _V_<sub>s</sub> is connected to one group _V_<sub>g</sub> as a group member via _E_<sub>m</sub>. We then need to find an arrangement of n edges for each student _E_<sub>n</sub> where _E_<sub>n</sub> does not intersect with _E_<sub>m</sub>.


#### Existence Theorem
Given n, _V_<sub>s</sub>, _V_<sub>g</sub> and _E_<sub>m</sub> in a graph _G = (E,V)_, where s &ge; 2 and g &ge; 2, there exists at least one combination of edges _E_<sub>n</sub>.

#### Proof
By Induction on the number of students (s): 

From the problem description, we can also deduce
* g &le; s (there must be fewer or equal number of groups than students)
* n &lt; g (each student can evaluate at most g-1 groups)
* n is fixed for each student, not for each group. Groups may receive varying numbers of evaluations.

Let _P_ be the predicate: _Each student belongs to one group and will evaluate n other groups._

**Base Case**: s = 2 (two students), implies g = 2 (two groups). Each student can belong to only one group and can evaluate only one group. 

**Inductive Step**: Show that if P(s) is true then P(s+1) is true as well. Assume a graph with _s_ students and we add one more student. The new graph can be broken into two cases:
* **Case 1:** The new student is added with **a new group**. Since the student cannot evaluate his own group, we can swap with any existing student. The existing student is guaranteed not to belong to the new group, and the new student is guaranteed not to belong to the any existing group. This can be repeated for each n evaluations.
* **Case 2**: The new student is added to **an existing group**. Since n &le; g-1, we can assign simply assign the new student to n other groups and be guaranteed he won't need to evaluate his own group. 

P(s+1) follows, completing proof by induction. &block;


#### Balanced Theorem
Given a graph _G = (E,V)_ as described above, where _n &lt; g-1_ (each student can evaluate at most _g-2_ groups) and the largest group has fewer members than the other groups combined, there exists a balanced combination of edges _E_<sub>n</sub> where each group vertex has the same approximate degree (_degree_<sub>max</sub> - _degree_<sub>min</sub> < 1). 

To balance the number of evaluations received by each group, we preferentially assign new students to the groups with the lowest degree first. If s does not divide g (the number of students is not a multiple of the number of groups), then some groups will receive _degree_<sub>max</sub> evaluations and some will receive _degree_<sub>min</sub> evaluations where _degree_<sub>max</sub> - _degree_<sub>min</sub> = 1. Precisely _s mod g_ groups will have _degree_<sub>max</sub>.

#### Proof
By Induction on the number of students (s): 
_Following the predicate and base case above, we modify the inductive step as follows:

**Inductive Step**: Show that if P(s) is true then P(s+1) is true as well. Assume a balanced graph with _s_ students and we add one more student. The new graph can be broken into two cases:
* **Case 1:** The new student is added with a new group. Since the student cannot evaluate his own group, we can swap with an existing student from a group with _degree_<sub>max</sub>. Peer evaluators should then be shifted from any _degree_<sub>max</sub> group until _degree_<sub>new</sub> = _degree_<sub>max</sub>, as long as the largest group has fewer members than the other groups combined. This can be repeated for each n evaluations.
* **Case 2**: The new student is added to an existing group. Since n &lt; g-1, we can assign simply assign the new student to n other groups and be guaranteed he won't need to evaluate his own group. Preferentially assign new students to the groups with the lowest degree first to maintain the balance.

P(s+1) follows, completing proof by induction. &block;



## Computation
### Visualizing the Problem
As an example, we can represent a graph of ten students (_s_) in four groups (_g_), where each student is required to submit two evaluations (_n_). The matrices below have the four groups in rows, and ten students in columns. 

Our algorithm will work as follows:

1. Populate the graph with group memberships. Mark these with an 'x' so we don't try to assign a student to evaluate his own group.

    ```
    Group 1| x - - - - - - - - - | 0
    Group 2| - x x - - - - - - - | 0
    Group 3| - - - x x x - - - - | 0
    Group 4| - - - - - - x x x x | 0
     - - - - - - - - - - - - - - - 
    Total  | 0 0 0 0 0 0 0 0 0 0 
    ``` 

2.  Fill unassigned spots in the graph from left to right, top to bottom with until we reach the total number of evaluations (_n * s_), skipping over any students that are already giving two evaluations. At this point, group degrees are highly imbalanced, but student degrees have been locked at the desired target. 

    ```
    Group 1| x 1 1 1 1 1 1 1 1 1 | 9
    Group 2| 1 x x 1 1 1 1 1 1 1 | 8
    Group 3| 1 1 1 x x x - - - - | 3
    Group 4| - - - - - - x x x x | 0
     - - - - - - - - - - - - - - - 
    Total  | 2 2 2 2 2 2 2 2 2 2 
    ```

3. Rebalance the graph by shifting a student from the group with most evaluations to the group with the least. Repeat up to (_n * s_) times or until the graph is balanced.

    ```
    Round 1
    Group 1| x - 1 1 1 1 1 1 1 1 | 8
    Group 2| 1 x x 1 1 1 1 1 1 1 | 8
    Group 3| 1 1 1 x x x - - - - | 3
    Group 4| - 1 - - - - x x x x | 1
     - - - - - - - - - - - - - - - 
    Total  | 2 2 2 2 2 2 2 2 2 2 

    Round 2
    Group 1| x - - 1 1 1 1 1 1 1 | 7
    Group 2| 1 x x 1 1 1 1 1 1 1 | 8
    Group 3| 1 1 1 x x x - - - - | 3
    Group 4| - 1 1 - - - x x x x | 2
     - - - - - - - - - - - - - - - 
    Total  | 2 2 2 2 2 2 2 2 2 2 

    ...
    
    Round 7 (Balanced)
    Group 1| x - - 1 1 1 - - 1 1 | 5
    Group 2| - x x - - 1 1 1 1 1 | 5
    Group 3| 1 1 1 x x x 1 1 - - | 5
    Group 4| 1 1 1 1 1 - x x x x | 5
     - - - - - - - - - - - - - - - 
    Total  | 2 2 2 2 2 2 2 2 2 2 
    ```
 
 
### Pseudocode

```
 MATCH(S, G, n)
   INITIALIZE(S, G)
   t = n  * length(S)
   while !REBALANCE(S, G) and i < t
     i = i + 1
 
 INITIALIZE(S, G, n)
   t = n  * length(S)
   count = 0
   while count < t
     x = i % length(S)
     y = i / length(S)
     if !ISMEMBER(G[y], S[x]) and !ISASSIGNED(G[y], S[x]) and length(GETASSIGNMENTS(S[x]))<n
       ASSIGN(G[y], S[x])
       count = count + 1

 REBALANCE(S, G)
   gMin = G[0]
   gMax = G[0]
   for each group in G
     if length(GETASSIGNMENTS(group)) > gMax
       gMax = group
     else if length(GETASSIGNMENTS(group)) < gMin
       gMin = group
 
   if gMax = gMin or length(GETASSIGNMENTS(gMax))-length(GETASSIGNMENTS(gMin))<2
     return TRUE
 
   for each student in S
     if ISMEMBER(gMax, student) or ISMEMBER(gMin, student) 
       CONTINUE
     if ISASSIGNED(gMax, student) and !ISASSIGNED(gMin, student)
       UNASSIGN(gMax, student)
       ASSIGN(gMin, student)
       return FALSE
 
   return TRUE
```
