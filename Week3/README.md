<h1 align="center"><strong>Welcome to Week-3!</strong></h1>
FINALLY TURNING ON THE RL ENGINE GUYS !!!! Now that you have learnt the build the environment correctly, it is time to let the agent learn from interaction. In this week, we move from designing RL problems to solving them. You will learn tabular reinforcement learning algorithms that work across multiple games, not just Flappy Bird.

---

## Goal of this week:

1. Understand what value functions are and what an agent actually learns
2. Learn Temporal Difference (TD) learning  
3. Implement tabular Q-learning  
4. Understand exploration vs exploitation (my favorite topic btw)
5. Learn how hyperparameters affect learning
6. Apply same algorithm to multiple environments!!!!!

---

## **1. Value function : what the agent learns**

In reinforcement learning, the agent doesn't learn rules or strategies directly. Instead, it learns **VALUE FUNCTIONS**. Interesting question : what are they and how do they look like???

There are two important value functions we will be covering :
1. **State Value Function**
   
   V(s) is the expected future reward when starting from state s
2. **Action-value function**
   
   Q(s,a) is the expected future reward when starting from state s and taking action a

Now, if the agent knows Q(s,a), the optimal strategy is simply choosing the strategy with highest Q-value. Thus, an RL problem reduces to learning accurate Q-values.

---

## **2. Temporal Difference (TD) Learning**

Temporal difference learning updates value estimates **step-by-step**, instead of waiting for the episode to finish. The core idea is to learn from the difference between what you expected and what actually happened (if only I could do this irl). This difference is called the **TD ERROR**.

TD target for Q-learning:

*target = reward + gamma * max(Q(next_state, all_actions))*

Let's break down this line a little bit. For an overview : this line simply answers the quesition of how good do I think this action was, based on what just happened and what will happen now. 

1. *reward*
   
   This is what just happened. Did I crash? negative reward. Did I survive? small positive reward. Did I pass a pipe? large positive reward. You can view it like an immediate feedback of the action. But this alone isn't enough sadly, because some actions are good now but bad later (*cry*).
   
2. *Q(next_state, all_actions)*
   
   View it as "from the next state I land in, how good is my future if I act optimally?". max(...) assumes that I take the best possible action in the future (remember that a good action means larger Q!!!)
   
3. *gamma*
   
   You might be thinking why this gamma multiple exists. *gamma* is called the DISCOUNT FACTOR. It answers the question "How much do I care about the future compared to now?". I like to understand it from its extremeties :
   
   1. gamma = 0 : I only care about immediate reward
   
   2. gamma = 1 : I care a lot about long term survival
      
  Now, in Flappy, long term survival matters a LOT!! I mean I want my birdie to fly longer :) So, we typically use gamma = 0.99 which means future rewards matter, but slightly less than immediate ones.

Why do we call this a target though? Because we are saying Q(curr_state, action) should move towards this value, not jump to it. That's why the full update equation must be :

*Q(s,a) <- Q(s,a) + alpha (target - Q(s,a))*

where *alpha* is how aggressively I correct myself.

---

## **3. Q-learning (CORE OF THIS WEEK)**

###Q-learning update rule:

*Q(s,a) <- Q(s,a) + alpha (reward + gamma * max(Q(next_state, all_actions)) - Q(s,a))*

where
1. *alpha* : learning rate
2. *gamma* : discount factor

Oh wait! We already covered this in section 2!!

But, why do we love Q-learning? It's model-free, off-policy and tabular! It doesn't need enviroment dynamics, transition probabilities or future knowledge! This means I can use it across many games. An elaborated explanation might be that very game provides :
1. states
2. actions
3. rewards
4. next states

Thus, this same beautiful algorithm works for 
1. multi-armed bandits
2. gridworld
3. frozenlake
4. taxi-v3
5. flappy!!!!

**only environment changes, not the learning algorithm** 

*mind-blown*

---

## **4. exploration vs exploitation**

An agent often faces a dilemma : Do I choose the best-known action or do I try something new? This is called the exploitation vs exploration. Pure exploitation means we are stuck in sub-optimal behavior but pure exploration means that there is no convergence. What do we do?

### **epsilon-greedy strategy**

With probability *epsilon*, we take a random action (exploration) and with probability *1 - epsilon*, we take the best-known action (exploitation). Over time, we decay *epsilon*, so the agent explores now and exploits later. Makes sense, doesn't it?

---

## **5. Discount factor (gamma)**

The *discount factor* controls how much the future matters 

*sighs silently*

   1. gamma = 0 : I only care about immediate reward i.e. short-term planning
   2. gamma = 1 : I care a lot about long term survival i.e. long-term planning

Game intuition:
- Bandits : γ ≈ 0  
- GridWorld / Taxi : γ ≈ 0.9  
- Flappy Bird : γ ≈ 0.99

(if you need future insight on why we chose this, feel free to ask us!!)
---

## **6. Learning rate (alpha)**

The learning rate controls **how aggressively updates are applied**.

- Large α : fast learning, unstable  
- Small α : stable learning, slow  

For tabular Q-learning, constant α values usually work well.

## **7. On-Policy vs Off-Policy Learning**

- **On-policy**: learns the policy it follows  
- **Off-policy**: learns the optimal policy regardless of behavior  

As we discussed before, Q-learning is **off-policy** because exploration may be random and updates assume greedy future behavior.

## **8. Training loops and episodes**

Learning proceeds in episodes. (omg new term what is that)
An episode is

reset -> act -> observe -> update -> repeat -> terminal

Basically, an episode is one complete attempt at the task. Formally, an episodes starts at reset() and ends at done=true. 

Every RL-algorithm boils down to:

```
RESET environment
WHILE episode not done:
    observe state
    choose action
    take action
    receive reward + next_state
    update learning rule
END episode
```

But, WHY do we add this episodes logic? Why do we even need episodes? Can't we learn once?

Episodes give learning boundaries :
1. They define success vs failure.
2. They limit how far credit assignment needs to go.
3. They let us measure progress (reward per episode)

Without episodes, rewards mix across unrelated trajectories, learning becomes unstable and evaluation is sort of meaningless. 

### Training vs evaluation

1. TRAINING MODE : goal is to improve the Q-table.
   - exploration on
   - epsilon > 0
   - random actions are allowed
   - learning updates enabled
   - rendering is off for speed
2. EVALUATION MODE : goal is to measure how good the learned policy actually is.
   - exploration off
   - epsilon = 0
   - always greedy actions
   - no learning updates
   - rendering is on for speed
  
If you evaluate with exploration ON, the agent behaves randomly, performance looks bad even if learning succeeded.

If you train with rendering on, training is 10-100x slower but you might think RL is slow even though it is not.

## **9. Convergence and failure models**

Here we answer the question "why is my agent not learning?" :(

*Convergence* means Q-values stabilize, policy stops changing much and performace plateaus at a good level. It does not mean perfect play, no variance and identical episodes. Intuitively, Q-learning converges if 
1. every state-action pair is visited many times
2. environment is markov
3. learning rate is reasonable
4. exploration decays slowly

Break any one and learning fails.

### COMMON FAILURE MODES 

1. **nothing is learning**
   
   *Symptoms*
     - rewards flat
     - Q-value near 0
     - random behavior
       
   *likely causes*
     - bad state (not markov)
     - rewards are too sparse
     - epsilon too small too early
       
   *fixes*
     - add velocity or future relevant info
     - add a small survival reward
     - slowly decay epsilon
       
2. **it learnt SOMETHING but the something is dumb**
   
   *Symptoms*
     - hovering
    - avoiding goal
     - weird behavior
       
   *likely causes*
     - reward hacking
     - bad reward scaling
     - overpowered shaping
   
   *fixes*
     - reduce shaping magnitude
     - check objective alignment
       
3. **it learnt early but stopped learning**
   
   *Symptoms*
     - initial improvement
     - early plateau
       
   *likely causes*
     - epsilon decayed too fast
     - agent stopped exploring
     - sub-optimal policy
       
   *fixes*
     - slow epsilon decay
     - higher min epsilon
     - restart training with optimal params
   
4. **rewards oscillate wildly**
   
   *Symptoms*
     - huge variance
     - Q-values explode
     - unstable curves
       
   *likely causes*
     - learning rate is too high
     - reward magn is too large
   
   *fixes*
     - reduce alpha
     - normalize rewards
       
5. **evaluation is worse than training**
   
   *likely causes*
     - evaluation with epsilon > 0
     - bad logic
       
   *fixes*
     - force epsilon = 0 in eval
     - check logic


In short, instead of asking why Q-learning doesn't work, try focussing on what assumption did we break.

## **10. Resources**
To learn more about Q-learning you can refer to [Q-Learning](https://www.geeksforgeeks.org/machine-learning/q-learning-in-python/)
