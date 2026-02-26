> Markov Decision Process: Deep Dive

### Refresher

- The Markov Property: 

$$\text{A state is markov iff} \quad P[S_{t+1}|S_t] = P[S_{t+1}|S_1,S_2,...,S_t]$$

- Memoryless in a way: the future is independent of the past given the present

### Intro

- MDPs formally descrive an env for RL
- Starting with fully observable (MDPs not POMDPs) - current state fully characterizes the process

★ Almost all RL problems can be formalized via an MDP
$\rightarrow$ Partially observable problems can be converted into MDPs

### State Transition Probability Matrix

For any set of states with the Markov property, the probability of transition from State $s$ to $s'$ can be defined via the state transition probability function:
$$P_{ss'} = P[S_{t+1} = s'|S_t = s]$$

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
> P: State transition Probability functiopn / Matrix such that $P_{ss'} = P[S_{t+1} = s'|S_t = s]$

While $(S,P)$ lacks actions and rewards, it captures the dynamics of the env. 

$\rightarrow$ Sampling from the dynamics you can create variable length sequences of states, i.e. random sequences drawn from the probability distribution. 

> What is the sequence called if the MP is a tuple of S and P? I seem to say that an MP is both a sequence of states with the Markov property and the tuple $(S,P)$. 