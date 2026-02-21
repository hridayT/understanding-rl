> Reinforcement Learning as the study and modelling of decision making. 

#### Crucial components of RL
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

#### Key Constructs and notations

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

#### State

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


