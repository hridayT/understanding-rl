> Markov Decision Process: Deep Dive

### Refresher

- The Markov Property: 

$$\text{A state is markov iff} \quad P[S_{t+1}\mid S_t] = P[S_{t+1}\mid S_1,S_2,...,S_t]$$

- Memoryless in a way: the future is independent of the past given the present

### Intro

- MDPs formally descrive an env for RL
- Starting with fully observable (MDPs not POMDPs) - current state fully characterizes the process

★ Almost all RL problems can be formalized via an MDP
$\rightarrow$ Partially observable problems can be converted into MDPs

### State Transition Probability Matrix

For any set of states with the Markov property, the probability of transition from State $s$ to $s'$ can be defined via the state transition probability function:
$$P_{ss'} = P[S_{t+1} = s'\mid S_t = s]$$

This then leads to a state transition probability matrix for all state pairs (each row sums to 1):
$$P = \begin{pmatrix} P_{11} & \cdots & P_{1n} \\ \vdots & \ddots & \vdots \\ P_{n1} & \cdots & P_{nn} \end{pmatrix}$$
$$\rightarrow \sum_{i=1}^{n}P_{xi} = 1, \forall x \in [1,n] $$

### Markov Process

> ★ You can create sequences of states, Markov Processes, by sampling from the matrix of state transition probabilities. 

> A Markov Process then is a sequence of States with the Markov Property. A memoryless, random process. 
→ memoryless: the present Markov state allows you to forget all previous states
→ random: states sampled from the transition probability martix / function

> **Defn:** A Markov Process (or Markov Chain) is a tuple $(S,P)$ where:
> S: A finite set of states
> P: State transition Probability functiopn / Matrix such that $P_{ss'} = P[S_{t+1} = s'\mid S_t = s]$

While $(S,P)$ lacks actions and rewards, it captures the dynamics of the env. 

$\rightarrow$ Sampling from the dynamics you can create variable length sequences of states, i.e. random sequences drawn from the probability distribution. 

> What is the sequence called if the MP is a tuple of S and P? I seem to say that an MP is both a sequence of states with the Markov property and the tuple $(S,P)$. 

### Markon Reward Process

> A Markov Process with value judgement --> How good is it to be in each state?

$$MRP = (S,P,R,\gamma) \quad \text{where} \\
\; R \; \text{is an "immediate" reward function} \quad R_x = E[R_{t+1}\mid S_t = s] \\
\gamma \text{is a discount factor} \quad \gamma \in [0,1]$$

- What you really want to know is not just the immediate reward but the total expected future reward from that state  
- $\rightarrow G_t: \; \text{total discounted reward from time} \; t$
$$G_t = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1}$$

→ $G$: Goal, RL is an attempt at maximizing return
→ $\gamma$: Tells you the "present value of future rewards" $\rightarrow$ how much do you care about future rewards?
- $\gamma = 0$ $\Rightarrow$ maximally shortsighted/myopic
- $\gamma = 1$ $\Rightarrow$ all rewards matter going into the future: long term

★ Note: No expectation in the above formulation of return as this sequence of rewards is one random sample from the MRP across the underlying MP (sequence of states).

> Why discount?  

$\Rightarrow$ Avoids infinite returns in cyclic Markov Processes  
$\Rightarrow$ Models uncertainty as we don't quite know how the future reward will play out  
$\Rightarrow$ Mathematically convenient as it bounds the reward  
$\Rightarrow$ Cognitive model of biological decision making as humans/animals show some preference for immediate reward  

Note:
- Immediate rewards also accrue over time 
- You could proceed with undiscounted iff all sequences terminate

<u>Expected Return</u> at time step t is the mean across all future possible sequences.  
Changing $\gamma$ can thus alter the outlook of agiven state significantly.  

### Towards the Bellman Equation

> The value function (expectation of total future reward) can be decomposed into two parts:  
- Immediate Reward $R_{t+1}
- Discounted value of following states / future reward, $\; \gamma v(S_{t+1})$

$$
\begin{aligned}
v_{\pi}(s) &= E[G_t \mid  S_t = s] \\
     &= E[\sum_{k=0}^{\infty}\gamma^k R_{t+k+1} \mid  S_t = s] \\
     &= E[R_{t+1} + \gamma (R_{t+2} + \gamma R_{t+3} + ...) \mid  S_t = s] \\
     &= E[R_{t+1} + \gamma G_{t+1} \mid  S_t = s] \\
     &= E[R_{t+1}] + E[\gamma G_{t+1} \mid  S_t = s] \\
     &= E[R_{t+1}] + \gamma E[ G_{t+1} \mid  S_t = s] \\
     &= E[R_{t+1}] + \gamma E[E[ G_{t+1} \mid  S_t = s]] \quad \text{Law of iterated expectations} \\
     &= E[R_{t+1}] + \gamma E[ V(S_{t+1}) \mid  S_t = s] \\
     &= E[R_{t+1} + \gamma V(S_{t+1}) \mid  S_t = s] \quad \text{Recursive and later opens the door to iterations for dynamic programming}\\
\end{aligned}
$$

### Bellman Equation for MRPs

$$v(s) = E[R_{t+1} + \gamma V(s_{t+1}) \mid  S_t = s]$$

$\rightarrow$ This is akin to taking a step forward and understanding the value at that step. A lookahead. 
$\rightarrow$ As many steps are possible in the next step and we have the state transition probability function, 

$$v(s) = R_{t+1} + \sum_{s' \in S} P_{ss'}v(s')$$

The one step lookehead. 

### Bellman in Matrix form, vector representation

 $$V = R + \gamma PV$$

$$
\begin{pmatrix} V(1) \\ \vdots \\ V(n) \end{pmatrix} = \begin{pmatrix} R(1) \\ \vdots \\ R(n) \end{pmatrix} + \gamma 
\begin{pmatrix} P_{11} & \cdots & P_{1n} \\ \vdots & \ddots & \vdots \\ P_{n1} & \cdots & P_{nn} \end{pmatrix}
\begin{pmatrix} V(1) \\ \vdots \\ V(n) \end{pmatrix}
$$

$$\begin{aligned} 
V(1) &= R(1) + \gamma [P_{11}V(1) + P_{12}V(2) + ... + P_{1n}V(n)] \\
&= R(1) + \gamma [\sum_{k=0}^{n-1}P_{1 k+1}V(k+1)]
\end{aligned}
$$

### DP Refresh: Bellman Ford for shortest distance

> Shortest distance between and 2 vertices in a weighted graph (can handle negative weights)  

$$d[v] = min_{u \in N(v)}(d[v], d[u] + d[u,v])$$

This update in Bellman Ford is carried out for each vertext $v \in V(G)$ in each iteration and you iterate $\mid V\mid  - 1$ times such that by then all optimal distances have been discovered. 

> Bellman Equation

$$V(s) = e[R_{t+1} + \gamma V(S_{t+1}) \mid  S_t = s]$$

### Solving the Bellman Equation for MRPs

$$\begin{aligned} 
V &= R + \gamma PV \\
\implies R &= V - \gamma PV \\
&= V(1 - \gamma P) \\
\implies V &= R(1 - \gamma PV)^{-1} \quad \text{matrix inversion}\\
\end{aligned}
$$

Bellman equation for MRPs is thus linear and solvable $O(n^3)$. 
> - Evaluating rewards is solvable via this linear equation however <u>maximising</u> rewards is another thing altogether and more complex. 
> - While matrix inversion is computationally expensive, there exist iterative methods for large MRPs such as Dynamic Programming (lecture 3), Monte Carlo sim/eval, Temporal Difference learning

★ All of these are building blocks for what we are really looking to model and solve: decision making.

### Markov Decision Process (MDP)

→ MRP with decisions  
→ We spoke of sampled transitions and state sequences but attached no agency to the agent's behaviour. Random sampling is not decision making. There was no explicit modeling of decisions / actions. 
→ MDP is a tuple $(S,P,A,R,\gamma)$ where,  
$\rightarrow S: \text{finite set of States}$  
$\rightarrow A: \text{finite set of Actions}$  
$\rightarrow P_{ss'}^{a}: \text{state transition probability matrix} \quad P_{ss'}^{a} = P[S_{t+1} = s' \mid  S_t = s, A_t = a]$  
$\rightarrow R_s^a: \text{Reward function} \quad R_s^a = E[R_{t+1} \mid  S_t = s, A_t = a]$        
$\rightarrow \gamma: \text{discount factor} \quad \gamma \in [0,1]$

Now in order to make decisions, we need a way to formally capture the choice of actions.  
★ Policy

> A policy $\pi$ is a distribution over actions given states. (stochastic policy)

$$\pi(a\mid s) = P[A_t = a \mid  S_t = s]$$

→ Policy defines the behaviour of an agent.  
→ Remember: given the Markov property, MDP actions depend on the current state (not the history).   
$\implies$ As all that matters is the "current" state, the fact that it is timestep "t" is irrelevant. The State captures all that needs to be captured.  

$$A_t \sim \pi(\cdot \mid s), \quad \forall t > 0$$
$$\pi(a\mid s) = P[A_t = a \mid  S_t = s], \quad \forall t > 0$$

### MDP → MRP → MP

Given an MDP = $(S,P,A,R,\gamma)$ <u>and</u> a policy $\pi$,  
- the policy lets you draw a sequence of states via actions (actions take you from one state to the next so a sequence of actions draws a sequance of states).  
- the state sequence so obtained, $S_1, S_2, ...$ is a Markov Process $(S,P^{\pi})$. 
     - here the state sequence defines the set of actions you are going to follow, i.e. a process. 
- the state and reward sequence $S_1, R_1, S_2, R_2, ...$ is a Markov Reward Process $(S,P^{\pi}, R^{\pi}, \gamma)$ defined by the policy $\pi$. Where:
     - $\displaystyle P_{ss'}^{\pi} = \sum_{a \in \mathcal{A}} \pi(a \mid s) P_{ss'}^a$
     - $\displaystyle R_{s}^{\pi} = \sum_{a \in \mathcal{A}} \pi(a \mid s) R_{s}^a$
- These two above capture the average dynamics. Averaged across all that can be done given the policy, the reward and state transition probability function are created. 

> This way you can always "flatten" an MDP → MRP → MP given a policy. 

> Why do rewards not feature in the policy formulation for rewards are what you are after? 
- As the state captures all you need to know of future state evolution, and as rewards lie in the future or the past, it is the current / present state which the policy considers for optimal next actions.  
- The way you pick the ideal action and the way you finetune your policy are deeply dependent on return, expected reward. So the reward is implicit and the goal but not available for decision making itself.  

### State and Action Value Functions (v and q) | Given a Policy

State-value function $v_{\pi}(s)$ of an MDP is the expected return starting from state $s$ and following policy $\pi$. 

$$v_{\pi}(s) = E_{\pi}[G_t \mid S_t = s], \quad \text{expectation when we sample all actions according to the policy}$$

Action-value function $q_{\pi}(s,a)$ is the expected return from state $s$, taking action $a$, and then following policy $\pi$.  

$$q_{\pi}(s,a) = E_{\pi}[G_t \mid S_t = s, A_t = a]$$

$\rightarrow$ a lot like state value apart from the immediate action conditional which defines that first lookahead in the action from state s.

> Optimising an MDP is optimizing Q. 

### Decomposing the state and action value functions as per the Bellman Equation

$$v_{\pi}(s) = E_{\pi}[R_{t+1} + \gamma v_{\pi}(S_{t+1}) \mid  S_t = s]$$
$$q_{\pi}(s,a) = E_{\pi}[R_{t+1} + \gamma a_{\pi}(S_{t+1}, A_t+1) \mid  S_t = s, A_t = a]$$

★ Follow policy $\pi$ from there onwards.  

> How do $v_{\pi}(s)$ and $q_{\pi}(s,a)$ relate to one another?  

- $q_{\pi}(s,a)$ is really the value of each action from state $s$ & is thus a component of all future value.  
- $s \mapsto v_{\pi}(s) \xrightarrow{a} q_{\pi}(s,a), \; \forall a \in A$  
- $$\implies \boxed{v_{\pi}(s) = \sum_{a \in A} \pi (a \mid s) \; q_{\pi}(s,a)}$$

$q_{\pi}(s,a)$: "q-value" / "action value"

★ Now remember that you do not control the env. You choose your actions and the env emits rewards & states / observations.  

→ $q_{\pi}(s,a)$ tells us the value of taking action $a$ at state $s$. However, in our MDP it is the state transition probability function $P_{ss'}^a$ and Reward function $R_x^a$ which step in once you take an action. 

$q_{\pi}(s,a) \xrightarrow{R_s^a \; P_{ss'}^a} v_{\pi}(s'), \; \forall a \in A$  

$$\implies \boxed{q_{\pi}(s,a) = R_s^a + \gamma \sum_{s' \in S} P_{ss'}^a v_{\pi}(s')}$$

$$\implies v_{\pi}(s) \xrightarrow{a \; \pi(a \mid s)} \; q_{\pi}(s,a) \; \xrightarrow{R_s^a \; P_{ss'}^a} \; v_{\pi}(s'), \; \forall a \in A$$

