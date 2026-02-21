> Reinforcement Learning as the study and modelling of decision making. 

### Crucial components of RL
---

- No direct supervision --> only a reward signal which may operate on the process and/or the outcome
- Often delayed feedback
    - The intermediate steps / actions / predictions / tokens do not immediately receive a reward
    - Intermediate steps can be optimal / suboptimal and yet the final reward may not directly speak to their correctness
    - This leads to the Credit Assignment Problem wherein upon receiving a reward you are to determine what exactly to reward / encourage. 
    - In sufficiently explorative sessions / settings broadcasting the reward to all intermediate steps is likely to be extremely noisy 
- Time!
    - Non-i.i.d (independent and identically distributed) like with timeseries but even more involved
    - The input is not just dependent on past data / stream / input but also influenced by the agent
    - The agent interacts with the world, impacts the world, and observes this world

Reward: $r_t$: scalar feedback signal

> Objective: $$\max \sum_{t=0}^{T-1} r_t$$, where T is the length of the episode / timesteps

Core principle: All goals can be described as the maximisation of cumulative reward
- This is just a formulation 
- Important to note perhaps that some rewards matter and some don't. At the level of the formulation you can then say the "reward" can take that into account
- "described" does a lot of heavy lifting here

→ This framing helps build a unifying framework which can address all of these different problems within the same formalism and thus address it via the same agent / set of ideas. 

#### Sequential Decision Making

Goal: Select actions to maximise total future reward
- As actions can have long term consequences and rewards can be delayed, the idea of maximising total reward does not immediately devolve into a greedy search for reward (immediate gains). 
- Occasionally better to explore now and exploit later for greater total reward

### Key Constructs and notations
---

Note: Gets a little messy here as jargon from the field of LLMs and agents today and the study of RL at large appear to depart a little from one another. All comes together later ( I think ). 

- Observations: $o_t$
- Actions: $a_t$
- Reward: $r_t$ 
- State: $s_t$

An agent:
- Interacts with its env
- Does not have control over the env
- Influences the env via its interactions

The env:
- Receives actions
- Emits Rewards and Observations (slightly inelegant framing to say it "emits" observations but it fundamentally changes state and the new state can be observed)

An interesting way to look at this:
- The agent selects actions
- The env selects observations and rewards
    - The env can thus be simulated and potentially active elements of the env (such as other players) can be agents such as simulating users for dynamic dialogue. 

History: Sequence of observations, actions, rewards (also the context)

Note: What happens next depends on the context / history

### State
---

This begins to get tricky but will be clear in a bit. 

The <u>**State**</u> is a summary of the information user to dewtermine what happens next. In a sense this is circular, it is the current state and the next state arises out of it + the actions of the agent and the response to the env. 

The State is a function of the history

$$S_t = f(H_t)$$

$S_t^e$: State of the env  
$S_t^a$: Internal representation of the state from the agent

As the entire state of the env (the full state of the agent's world / surroundings) is not fully visible / accessible to the agent, the agent thuys works on its representation. 

Note: Even if some of the state were known, some of it would be irrelevant to the decisions the agent needs to make. Not just distractors, just purely irrelevant artifacts. 

The internal state for the agent is the state we are most concerned with here. 

> In the tool use scenario, the state of the world at any instant can be inpsected via tool calls. What is the "internal representation" here? The context? Perhaps what is known to the agent. 

★ You can design the function which is employed to convert the history of actions, rewards, and observations, into a useful state. 

$$S_t = f(H_t), \quad \text{where} \; H_t = [A_1, O_1, R_1, ..., A_t, O_t, R_t]$$

★ Markov State (Property)
$$\text{A state $S_t$ is Markov iff} \quad P[S_{t+1}|S_t] = P[S_{t+1}|S_1,...,S_t] \\
\text{i.e. the current state captures all you need to know to capture the full state space of possible future states}$$

> Context Window vs State: State is a function of context and the context is the history. So in the vanilla case of no context management the context is the LLM's history. 

What the Markov property really says is that if you take decisions based on S_t, those decisions can still be optimal. You still have to figure out what actions to take and how to get to the optimal states. 

Example of a state which is not markov: insufficient info. Moving object and all you know is the position. You can't take decisions on manoeuvres without knowing velocity / momentum, etc. 

Examples of Markov States
- The env state is Markov as that is the state the env is in and all future states arise out of it. 
- The history is Markov as well as that is what the env uses to emit observations, rewards, & get to the next state? 
    - What about elements of the env the model has not explored yet and thus the info present in its history is incomplete? 
    - Does this include info available to the model which has not yet been accessed? 
    - As the set of actions the model can take include theactions of further inspection, optimal states still remain in reach given the history

★ The agent state you build is critical to its ability to model the world in it

> The agent state vs the agent's policy (state to action)?

Observability of Envs and Impact on States:
- Fully Observable Environments: agent directlt observes env state:
$$O_t = S_t^a = S_t^e = S_t$$

Markov Decision Process (MDP) $\rightarrow$ Full observability of markov states

- Partially Observable Environments: The agent indirectly observes the env. Partial info. 
$$S_t^a \neq S_t^e$$

Partially Observable Markov Decision Process (POMDP) $\rightarrow$ Full observability of markov states

Here the agent must construct its own state representation:
- Vanilla Complete History: $S_t^a = H_t$
- Bayesian / Probabilistic: $S_t^a = (P[S_t^e = S_1],...,P[S_t^e = S_n])$
- RNN: model the state transformation: $S_t^a = \sigma(S_{t-1}^aW_s + O_tW_o)$

### Components of an RL Agent
---

Core Components:
- <u>**Policy**</u>: agent's behaviour function $\rightarrow$ maps state to action
- <u>**Value Function**</u>: measure of how good a given state and action are
- <u>**Model**</u>: agent's representation of the env

> ★ Policy

→ Maps State to Action  
→ At large:  
$\quad$ → Deterministic: $a = \pi(s)$  
$\quad$ → Stochastic: helps explore more of the state space via non-determistic actions, $\pi(a|s) = P[A=a|S=s]$

> ★ Value Function

→ Prediction / expectation of future reward
→ As it gives you expectation of future reward, you can use it to choose between actions
→ The expected total reward is dependent on the policy

$$V_{\pi}(s) = E_{\pi}[R_t + \gamma R_{t+1} + \gamma R_{t+2} + ... | S_t = s]$$

$\rightarrow$ expectation of total future reward given the current state and policy. 

> Why do you need a policy $S \rightarrow A$ & a value function, value of $S$? Shouldn't the policy $\pi$ encode the understanding of action choices and vice versa? Yes! As revealed later. 

> ★ Model

→ Attempts to predict what the env will do next.  
→ Transition Model: predicts the next state  
→ Reward Model: predicts the next immediate reward (this is the reward model from RLHF where to understand the env it learns from human feedback and then can mimick the env).  

State Transition Probability Function:  
$$P_{ss'}^a = P[S_{t+1}=s'|S_t=s,A_t=a]$$

Reward (given Action $a$ at State $s$):  
$$R_s^a = E[R_{t+1}|S_t=s,A_t=a]$$

$\rightarrow$ Model Free RL: no explicit modelling of transition and reward
$\rightarrow$ Model Based RL: explicit modelling of transition / env dynamic and reward 

### Maze Example
---

- Each cell / position is a state. 
- Optimal policy $\pi(s)$ points in the direction of the shortest path to the goal
- Ideal value function $V_{\pi}(s)$ represents the number of steps to the final goal (negative reward as steps are to be minimized)
- What about the model? The agent may have an internal model of the env (maze)
    - Transition / Dynamics: how actions tend to change the state
    - Rewards: reward given a state
    - Note: The agent's internal model may be imperfect

> Notice how the optimal Policy and the ideal Value Function are identical in the above scenario. If you have a good enough value function, that is your policy in a sense.

### Taxonomy of RL Agents
---

- Value Based
    - No policy (implicit)
    - Value Function
- Policy Based
    - No Value function
- Actor Critic
    - Policy and Value function
    - Value function helps with credit assignment (Advantage computation)
    - 2x Memory
- Model Free
    - Policy and/or Value function
    - No Modelling of env
- Model Based
    - Policy and/or Value function
    - Model

### On RL and Planning
---

> Two fundamental problems in sequential decision making. 

- The RL Problem:
    - The env is initially unknown
    - The agent interacts with the env
    - Improves policy
- The Planning Problem:
    - A **model** of the env is known (simulation?)
    - The agent works with the model (with no external interaction)
    - The agent improves its policy

★ RL is like trial and error  
★ Explore vs exploit  
★ Optimal policies can only be discovered via exploration. Too much exploration and no policy gets implemented. Reward thins out.   
★ Exploration tells you more about the env  
★ Exploitation maximises reward given known info  

> Prediction and Control: Prediction evaluates the future given a policy, Control optimises the future to find the best policy