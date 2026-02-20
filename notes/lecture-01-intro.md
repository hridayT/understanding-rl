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
