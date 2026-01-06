<h1 align="center"><strong>Welcome to Week-2!</strong></h1>

In Week-2, we’ll focus on formalizing environments for Reinforcement Learning. Before an agent can learn, the environment must be correct. We do not train agents yet. Instead, we learn how to correctly define states, actions, rewards and termination conditions. A poorly designed environment makes learning impossible, no matter how advanced the algorithm is.

---

## Goal of this week:

1. Formally define an RL problem as a Markov Decision Process (MDP)
2. Identify what information must be included in the state
3. Understand the Markov property and partial observability
4. Design meaningful reward functions
5. Validate an environment using random policies
6. Predict RL failure modes before training

---

## **1. Markov Decision Processes (MDPs)**
A reinforcement learning problem is formalized as a Markov Decision Process (MDP) consisting of :
1. **STATES** (S) : this is what the agent observes about the environment
2. **ACTIONS** (A) : this is what the agent can do
3. **TRANSITION DYNAMICS** (P) : this is what the environment changes after an action
4. **REWARD FUNCTION** (R) : this is the feedback given to the agent
5. **DISCOUNT FACTOR** (Y) : this is how much future rewards matter

The Markov property means that the current state must contain all information needed to predict the future. 

### Recommended Resources
- [David Silver — RL Lecture 1](https://youtu.be/2pWv7GOvuf0?si=s-y7y_4nIJhmRBJt)
- [David Silver — RL Lecture 2 (MDPs)](https://youtu.be/lfHX2hHRMVQ?si=w65XJL6522gbFLLq)
- [Sutton & Barto — Chapter 3 (MDPs)](https://web.stanford.edu/class/psych209/Readings/SuttonBartoIPRLBook2ndEd.pdf)

It is fine to just get a basic idea of this and not go into details of this.
---

## **2. State Design & the Markov Property**
THE RULE : a state must be sufficient
As an example, 
1. (bird_y) is not a sufficient state
2. (bird_y, velocity) is a better state but still not Markov sufficient
3. (bird_y, velocity, distance_to_pipe, gap_offset) is Markov

Trick : If two identical states can lead to different futures, the state is not Markov.

**Guide:** [Lilian Weng — MDPs and Partial Observability](https://lilianweng.github.io/posts/2018-02-19-rl-overview/#markov-decision-process-mdp)

---

## **3. Discretization**

Tabular Q-learning requires a finite set of states but real environments are continuous. Discretization converts continuous values into bins. It's evident that there thus exists a trade-off. If we make the values too fine, the Q-table becomes huge, the convergence is slow and the updates are sparse. If we make the values too coarse, the learning is fast, precision is lost. The goal is just enough resolution to make good decisions. 

**Flappy Bird insight** 

The bird doesn't need to know its exact pixel position. It needs to know
1. Am I above or below the gap?
2. Am I moving up or down fast?
3. Is the pipe close or far?

Thus, discretization encodes decision-relevant information, not perfect accuracy.

### Recommended Resource
- [Binning and Binarization | Discretization](https://youtu.be/kKWsJGKcMvo?si=O8iK2k3BJdxXzflI)

---

## **4. Reward Design**

The reward function defines what behavior is considered good. Agenets do not learn "survive", "be smooth", "look human", but they learn how to maximise the cumulative reward.

**Reward design principles**
1. Sparse rewards : given only when a major event occurs. Most steps give zero reward. Examples : +10 for passing a pipe, -50 for crashing, 0 anywhere else. Sparse rewards are attractive because they are very simple, there is no risk of reward hacking and they directly reflect the objective. However, they are hard in practice because the agent gets almost no feedback and learning is very slow. Thus, sparse rewards work well in very small environments, short episodes and deterministic dynamics. However, Flappy bird does not satisfy these conditions.
2. Dense rewards : provides frequent feedback, often every time step. Examples : +0.1 for staying alive each step, small penalty for moving away from each goal, small bonus for moving towards the goal. Dense rewards are helpful because the agent always gets feedback, learning signal propagates fast and exploration is easier. However, they can change the objective if not designed carefully. The agent may optimize the reward signal and not the intended task. This is called **reward hacking**. Thus, dense rewards must be small in magnitude, aligned with long-term goals and be carefully balanced.
3. Shaped rewards : adds auxiliary rewards to guide the agent toward good behavior. These rewards are not part of the original objective and exist only to help learning. Examples : bonus for staying near the center of the pipe gap, penalty for flying too high or too low, small reward for reducing distance to goal. Shaped rewards reduce exploration difficulty, give directional guidance and help the agent learn how to behave, not just what to achieve. In short, they act like training wheels. However, shaping if done poorly, can lock the agent into suboptimal behavior, prevent discovery of better strategies and encode human bias into policy.

In summary, sparse rewards define the goal, dense rewards accelerate learning, and shaped rewards guide behavior - but all of them can mislead if used carelessly.

---

## **5. Environment Validation with Random Policies**

Before training an agent, we must answer the question : “Is this environment sane?”
Random policies help us detect bugs, impossible tasks, mis-scaled rewards. If random agents die instantly, the task is too hard. If random agents survive forever, task is too easy. If rewards explode, reward bug. This step prevents wasting thousands of training episodes. 

---

## **6. Environment Stress Testing**

Small changes in environment can drastically affect learning. Stress testing checks sensitivity to gravity, sensitivity to reward scale and sensitivity to observation noise. An agent that only works for one precise setup is brittle. Understanding sensitivity builds intuition about what the agent is actually using and what variables dominate behavior.



