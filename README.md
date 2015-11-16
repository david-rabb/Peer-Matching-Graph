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
We can describe the classroom with a biparite graph _G = (E,V)_ with vertices for students _V_<sub>s</sub> and groups _V_<sub>g</sub>. Connecting the two types of vertices are two types of edges representing group membership _E_<sub>m</sub> and peer evaluators for each group _E_<sub>n</sub>. The total number of vertices is |_V_<sub>s</sub>| + |_V_<sub>g</sub>| where |_V_<sub>g</sub>| &le; |_V_<sub>s</sub>|. The total number of edges is |_E_<sub>m</sub>| + |_E_<sub>n</sub>| where |_E_<sub>n</sub>| = n |_V_<sub>s</sub>| (number of evaluations per student x number of students). 

To setup the problem, each student _V_<sub>s</sub> is connected to one group _V_<sub>g</sub> as a group member via _E_<sub>m</sub>. We then need to find an arrangement of n edges for each student _E_<sub>n</sub> where _E_<sub>n</sub> does not intersect with _E_<sub>m</sub>.


#### Theorem

#### Proof

## Algorithm
