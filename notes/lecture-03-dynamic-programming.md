> Dynamic Programming: Planning and Control
  
★ We have spoken of how to formalize RL via MDPs. However, we haven't actually explained how to solbe MDPs to identify $\pi_*$ which gets you the optimal value. 

### Introduction

Dynamic Programming (DP):
- Dynamic: temporal/sequential component to the problem
- Programming: Optimizing a "program" / a policy

→ Employed to solve complex problems by breaking into subproblems and solving those subproblems  
→ The solution to those subproblems is then combined to form the solution for the overarching problem.  

> The solution to a subproblem constitutes progress toward the solution to the larger problem.

DP is a general solution method for problems with 2 properties:
- Optimal Substructure:
    - Principle of optimality applies
    - Optimal solutions to subproblems are necessary for optimal solutions to the whole
    - $\implies$ optimal solutions can be obtained via decomposition into subproblems
- Overlapping subproblems:
    - Subproblems recur across the solution space
    - Solutions can be cached and reused to obtain the overall optimal solution
    - recall: Bellman-Ford $d[v] = \min (d[v],d[u] + d[u,v] \forall u \in N(v))$

> ★ MDPs satisfy both of these properties
$\rightarrow$ Bellman equation gives us recursive decomposition  
$\rightarrow$ Value function stores and reuses solutions for overlapping subproblems (subsequent states)

**Recall**: Planning is not the full RL problem as the dynamics are known
- The env is known - full perfect knowledge of the dynamics
- Improve policy by computing actions and rewards / return
- Consists of Prediction and Control

$\implies$ Planning in DP assumes full knowledge of the MDP (we know the env).

