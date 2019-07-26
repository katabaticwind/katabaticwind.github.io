---
layout: post
author: Colin Swaney
title: Reinforcement Learning Primer
date: 2019-06-01
categories: [research]
category: research
tags: [reinforcement learning]
---

This is the first in a series of introductory lectures on deep reinforcement learning.

I will mostly follow the classic text by Sutton and Barro [SB], but I will also rely heavily on Sergey Levine's lecture notes from his [course on deep reinforcement learning](http://rail.eecs.berkeley.edu/deeprlcourse/) taught at Berkeley.

## The Goal of Reinforcement Learning

The goal of reinforcement learning is to train computers to perform complex, dynamic tasks, which generally involve *planning*. From a mathematical perspective, reinforcement learning presents itself as a computational---at times heuristic---alternative to traditional control theory. Both fields offer solutions to the general problem of determining an optimal course of action in a dynamic, and possibly uncertain, environment---which I simply refer to a "control" problem. Such control problems are *complicated*, but we can break them down into two parts. First, there is a time series or stochastic processes component, which reflects how the system changes over time as well as how the systems responds to any possible action. Second, there is an optimization component, which involves determining how to act to alter---or control---these dynamics in order that the system achieves some desired outcome, or follows some optimal trajectory.

Traditional mathematical approaches to these types of problems represent some of the crowning achievements of applied mathematics. In some cases, they provide *exact* formulae describing what actions to take and when; for a surprisingly board range of problems, they prove that certain numerical methods generate approximate solutions that can be made arbitrarily close a ground truth solution. To give just one example, the "genius" of modern finance---the "astrophysics" that is derivatives pricing---is applied stochastic control. But for all the beauty of mathematical optimal control, it doesn't seem to have the flexibility required to solve many apparently simple real-world problems. The real world is messy, and it is in that messy world that reinforcement learning takes center stage.

![The control problem](/assets/img/control.png)

<!-- The real-world is messy, and it is often better to have a solution that is approximate, but robust, then one that is exact and brittle.  -->


## Agents & Environments
Reinforcement learning centers around "agents" and "environments". An agent is a computer that makes decisions and performs actions. The notion of an environment captures the world the agent observes and interacts with. Reinforcement learning generally considers decisions that have to be made over time, especially problems in which the actions an agent chooses at one step affect the environment in the following steps. The choice to perform action A prohibits the choice of performing action B at any particular time, and therefore influences what the agent can learn about the environment.

It's worth pointing out that "time" doesn't refer to "natural" time: it's just an ordering of events, which can have an arbitrary amount of time between each step. For example, time might count each time a user visits a web application, in which case steps might be spaced out randomly, or an agent might check the state of the world on a fixed schedule (e.g. once per second). The goal of reinforcement learning is not to model or predict the timing of events, but to determine how to respond to events as they occur.

Likewise, "state" should not be construed to mean a "natural" state of the world, but can be thought of abstractly. It *can* mean measurable/observable quantities like location and speed, but it can also refer to a "state of mind" or a "state of thought" [SB].


### Dynamics
The theory of reinforcement learning centers on a simple but quite flexible assumption regarding the way the environment works: we assume that whatever happens each time step depends only on the current state of the world and on our current choice of action. The rest of history is *irrelevant* to what happens this time step. In other words, it doesn't matter *how* we arrived at the current state, it only matters that we are in that state.

Mathematical, we say that the system follows a *Markov decision process* (MDP). Let's denote the state of world at time $$t$$ by $$s_t$$, our agent's action by $$a_t$$, the reward they receive by $$r_t$$, and the *new* state $$s_{t + 1}$$. The Markov assumption says that $$r_t$$ and the *next* state of the world $$s_{t + 1}$$ are determined by some probability distribution represented by the joint density

$$p(s_{t + 1}, r_t \ \vert \ a_t, s_t),$$

which completely describes the dynamics of the system---$$p$$ is the thing we need to know in order to understand how the environment works. But we haven't said anything about the nature of $$p$$ other than that it is a valid probability distribution. In many applications, $$p$$ might be a  deterministic---but extremely complicated---function. Nonetheless, the assumption means that *if* we know $$p$$, then we can make optimal decisions based on the current state of the world, and nothing else.

![mdp](/assets/img/mdp.png)

The figure above shows a canonical representation of the MDP. If you've studied recurrent neural networks, this diagram ought to look familiar--from a high level it's the same as a vanilla RNN. Alternatively, you might see this figure as a special form of hidden Markov model in which we add an additional "forcing" variable $$a_t$$ to the mix. Either way, the Markov assumption reduces the problem of understanding the dynamics of the system to that of learning a single distribution represented by $$p$$.

<!-- In the case of a hidden Markov model, $$p$$ is typically a separable function taking the form $$p(s', r \ \vert \ s, a) = f(r \ \vert \ s, a) \ g(s' \ \vert \ s, a)$$. In the case of vanilla RNN, $$p$$ is also separable, but $$f$$ and $$g$$ are represented by hidden layers instead of explicit probability distributions. -->

Now this assumption isn't really as restrictive as you might think (surely many interesting problems require us to know more than just the most recent history!), because we are always free to redefine what we mean by "state". For example, if you tell me that tomorrow's weather depends on the last two days of weather, and not just yesterday's weather, then I will simply change my definition of state from "the last day's weather" to "the last *two* days' weather". As [SB] puts it, "This is best viewed a [sic] restriction not on the decision process, but on the state." In other words, any process that we have a reasonable hope of controlling *will* satisfy the Markov property for *some* definition of the state.


### Rewards
Agents' learn optimal plans via reward signals designed and specified by us, their human overlords. Rewards can be understood by analogy with neurotransmitters such as dopamine, which signal the desirability of different outcomes in the human brain. Such neurotransmitters motivate us to perform actions that lead to or avoid particular outcomes through experience. In the case of reinforcement learning, researchers specify the amount of reward associated with the environment, and rewards are therefore arbitrary in some sense. On the other hand, the performance of a reinforcement learning algorithm is highly dependent on the choice of rewards, with poorly chosen rewards leading to agent's that fail to learn the desired outcome or becoming "addicted" to sub-optimal behavior.

There are sometimes natural choices for rewards. For example, an agent trained to play a two-player video game might earn a reward of one for winning, and negative one for losing, or it might earn rewards proportional to its score in a one-person game. In other cases the choice of rewards might be less obvious: what rewards should we specify for a robot learning to walk? In any case, from the computer's perspective rewards are just some numerical value; reinforcement learning algorithms encourage the computer to select actions that generate greater amounts of reward.

For example, in the typical reinforcement learning setting the agent's goal is to maximize the expected amount of total reward received:

$$ \max_{\{a_0, \dots, a_T\}} \mathbb{E} \left[ R \right] = \max_{\{a_0, \dots, a_T\}} \mathbb{E} \left[ \sum_{t = 0}^T \gamma^t r_t \right] $$

We refer to the quantity $$R$$ as the *return*, and the expectation is taken with respect to the transition probability $$p(s_{t + 1}, r_t \ \vert \ a_t, s_t)$$. The quantity $$\gamma$$ is called the "discount factor", and it imparts the agent with a time-preference over rewards: when $$\gamma < 1$$, the agent prefers to receive rewards early rather than later, all else being equal.

It's important to recognize that there is no aspect of *risk* in the above objective. From a behavioral perspective, it seems clear that agents' actions will generally reflect both risk and reward. For this reason the study of economics assumes that agents choose actions that maximize a so-called *utility* function $$U$$, which generally involves both the expectation and variance of rewards. For example, an investor might seek to maximize

$$U(R) = \frac{\mathbb{E}\left[ R \right]- b}{\text{var} \left[ R \right]}$$

reflecting the intuitive notion that for any fixed expected return, a lower risk outcome is preferable. This is a interesting topic because adjusting for risk tolerance is likely essential to making deep reinforcement learning solution palatable in many practical settings.

Reward design deserves more attention that I can give it here, but I feel it's important to reiterate [SB], who point out that rewards are not a good mechanism for encouraging solutions with certain properties. We don't use rewards to encourage a robot to learn sub-tasks because the agent might learn to *only* perform the sub-tasks, ignoring the task we actually care about! In other words, we want to focus agents on one goal, and let the agent figure out how to achieve that goal.

<!-- We can pass prior knowledge to agents through their policy or initial value function, for example, by placing restrictions on the shape of the policy function or by removing certain actions from the choice of possible actions in states where we know they are sub-optimal. **I think it is sort of in general interesting to compare the policy of (deep) RL algorithms to the true optimal policy in problems where we can derive (or numerically approximate to arbitrary accuracy) the optimal solution.** -->

#### Infinite vs Finite Horizon
Textbook presentations of reinforcement learning typically make a distinction between problems which eventually come to a finish---"finite horizon"---problems, and problems that continue indefinitely with non-zero probability--"infinite horizon" problems. The distinction has theoretical implications, but I view it as largely a distraction. The primary reason that the infinite horizon case is brought up is to motivate the idea of the discount factor mentioned above, which is optional in the finite horizon case, but  *required* in the infinite horizon case to guarantee that $$R$$ is well-defined.

From an economic perspective, discount factors can be motivated in either case. Discount factors tend to be less important in reinforcement learning because we aren't trying to model human decision-making, or we are trying to train agents to perform tasks where time preference plays an insignificant role. Nonetheless, if a reinforcement learning agent is used to learn an economic task, choosing an appropriate discount factor might be critical to finding an acceptable solution.


### Examples
Let's take a brief look at three environments. These examples are admittedly contrived, but they are useful for testing algorithms and simple to understand. Each of these examples is available in OpenAI's `gym` package.  

#### CartPole

![CartPole](/assets/img/cartpole.gif)

The pole-balancing problem is a classic problem in reinforcement learning. It works like this. Imagine a simple cart on a track. The cart can only move in one axis, from side-to-side. Now imagine that a long pole is attached to the cart via a hinge, so that as the cart moves the pole falls in the opposite direction. We start the environment by placing the pole is some random "not-quite-vertical" position, and the goal of the agent is to keep the pole from falling over (or not letting the pole fall past a certain angle, after which it is basically impossible to bring the pole back upright).

The only information that the agent needs to know is the position of the cart, $$x_t$$, and the angle of the pole, $$\theta_t$$. Therefore the state is two-dimensional: $$s_t = (x_t, \theta_t).$$ There are two available actions: "push left", and "push right" (assume that the agent can only push with some fixed amount of force). We say that the state space is continuous, while the action space is discrete. You could make the problem more complicated by allowing the agent to choose the direction to push *and* the amount of force to use. In that case, the action space would be mixed, the direction being discrete, and the force continuous.

This environment is extremely simple. We don't need any fancy algorithms to solve it, but it is useful for debugging: if it doesn't solve `CartPole`, it doesn't work. (I like to think of the inverse statement as being *almost* true: if it solves `CartPole`, it *probably* works--so if you're having problems with some other environment, maybe focus on errors in the environment-specific part of your code).

<!-- ```python
import gym
env = gym.make('Pong-v4')
env = gym.wrappers.Monitor(env, '/Users/colinswaney/Desktop/pong/', force=True)
env.reset()
env.render()
steps = 0
while True:
    state, reward, done, info  = env.step(env.action_space.sample())
    steps += 1
    if done:
        print(steps)
        break
env.close()
```

```shell
mkdir frames
ffmpeg -i *.mp4 -r 5 'frames/frame-%03d.jpg'
convert -delay 20 -loop 0 ./frames/*.jpg pong.gif
``` -->

#### LunarLander

![LunarLander](/assets/img/lunar-lander.gif)

A classic problem in optimal control considers how to land a rocket on (e.g. the moon). The goal is actually to land the rocket using the minimal amount of fuel (as otherwise you could always make a pillow-soft landing by burning through as much fuel as you want). Landing the rocket means that you arrive at a specific location (or within a specified region) with zero velocity and zero acceleration (and you don't crash). In a standard version of this problem--which can be solved analytically [E]--the rocket drops straight down under the influence of gravity alone, and the pilot/onboard computer only needs to determine how much upward thrust to provide. In that case, the state space is continuous and one-dimensional ($$y_t = $$ height), as is action space is action space ($$a_t = $$ thrust).

The `gym` version of this problem is a bit more interesting. First, the lander is dropped with a random state/orientation, so every run is different. Second, the lander is equipped with left-oriented and right-oriented thrusters in addition to a bottom thruster. The agent can only fire one of the thrusters (or none) at any instant, and the thrusters only have one setting. The state is described by eight variables:
- `x_position`
- `y_position`
- `x_velocity`
- `y_velocity`
- `angle`
- `angular_velocity`
- `left_leg_contact`
- `right_leg_contact`

The first six variables are continuous, and the last two are discrete (they just indicate whether the legs are on the ground or not).

#### Pong

![Pong](/assets/img/pong.gif)

Dominating video games is the application of reinforcement learning that has received the most attention in the last few years. Pong is one simpler video games to learn. Reinforcement learning researchers Atari games as a benchmark in research all the time because they offer up a wide range of tasks. The computer agent learns the game using essentially the same information that a human player has access to: a "visual" representation of the game, represented by a matrix of pixels. It can also perform any of the actions that a human can take. In pong, this means "do nothing" (also referred to as "NOOP" for "No Operation"), "move paddle up", or "move paddle down".

The following snippet of code demonstrates how to run a random policy for one episode using `gym`:

```python
import gym
env = gym.make('CartPole-v0')
env.reset()
while True:
    state, reward, done, info  = env.step(env.action_space.sample())
    if done:
        break
```

<!-- ## Highlights
Reward, return, episode, action, state, Markov decision process (MDP), partially observable Markov decision process (POMDP) -->


# Optimality
The goal of reinforcement learning is to train computers to perform tasks *well*. Ideally, the algorithms we use will lead to *optimal* decision-making. Let's take a moment to discuss a number of important ideas that will show up everywhere in the study of deep reinforcement, which are motivated by dynamic programming: the study of optimal planning in dynamic (unfolding over time) processses.

### Policies
First, an agent's actions are determined by a **policy**, which in generally specifies that probability of each possible action that our agent might take in each state of the world. For simplicity, let's suppose that the world is finite so that we can enumerate the states of the world $$s_t \in \{0, 1, \dots, K - 1\} = \mathcal{S}$$ as well as actions $$a_t \in \{0, 1, \dots, N\} = \mathcal{A}$$. For each $$a \in \mathcal{A}$$ and $$s \in \mathcal{S}$$, a policy defines the probability our agents performs the actions at step $$t$$:

$$ \pi(a \ \vert \ s) = p(a_t = a\ \vert \ s_t = s) $$

The policy defines a proper probability distribution, so we can use it to compute statistical quantities such as the expected reward at time $$t$$:

$$ \mathbb{E}_{\pi} \left[ r_t \ \vert \ s_t = s \right] = \sum_{a} \pi(a \ \vert \ s) \sum_{r_t, \ s_{t + 1}} p(r_t, s_{t + 1} \ \vert \ s_t = s, a_t = a) \ r_t$$

(The notation $$\mathbb{E}_{\pi}[x]$$ is shorthand for $$\mathbb{E} \left[ x \ \vert \ a_t \sim \pi(a \ \vert \ s) \right]$$. More generally, it means that *every* action is chosen according to $$\pi$$).

### Values
Reinforcement learning revolves around two functions associated with any policy. The *value* of a policy is expected return (sum of rewards) "under the policy":

$$ V^{\pi}(s) = \mathbb{E}_{\pi} \left[ R_t \ \vert \ s_t = s \right] = \mathbb{E}_{\pi} \left[ \sum_{\tau=t}^{T} r_{\tau} \ \vert \ s_t = s \right] $$

The *action-value* of a policy is the expected return if we perform an arbitrary action $$a$$ in step $$t$, and then follow the policy:

$$ Q^{\pi}(s, a) = \mathbb{E}_{\pi} \left[ R_t \ \vert \ s_t = s, a_t = a \right] $$

The only difference between this and the value function is the initial arbitrary action, and the two functions are related to one-another through the equations

$$ V^{\pi}(s) = \mathbb{E}_{\pi} \left[ Q^{\pi}(s, a) \right] $$

and

$$ Q^{\pi}(s, a) = \mathbb{E}_{\pi} \left[ r_t + \gamma V^{\pi}(s_{t + 1}) \ \vert \ s_t = s, a_t = a \right] $$

### The Bellman Equation
The *Bellman equation* demonstrates that the value of a given policy satisfies a particular recursive property:

$$ V^{\pi}(s) = \mathbb{E}_{\pi} \left[r_t + \gamma V^{\pi}(s_{t + 1}) \ \vert \ s_t = s \right] $$

In words, the expected value of following a policy can be broken into the expected reward next period and the expected *return* from all the subsequent periods, and the later is simply the value one period in the future. We need to take the expectation of the future value because the future state is random, and where we end up next period depends on the action we choose today.

This equation has an interesting property: it *defines* $$V^{\pi}$$ in the sense that the value function of $$\pi$$ is its *unique* solution. In fact, if there are a finite number of states—let's say $$K$$ to be concrete—then the Bellman equation is really a $$K \times K$$ system of equations, and $$V^{\pi} \in \mathbb{R}^K$$ is its solution, which we can perhaps see this more clearly by writing out the Bellman equation using probabilities:

$$ V^{\pi}(s) = \sum_a \pi(a \ \vert \ s) \sum_{r_t, \ s_{t + 1}} p(r_t, s_{t + 1} \ \vert \ s_t = s, a_t = a) \left[ r_t + \gamma V^{\pi}(s_{t + 1}) \right] $$

This equation holds for every $$s \in \mathcal{S}$$, and so for each $$s$$ we get an equation that involves known probabilities ($$\pi$$ and $$p$$) and the value in each state $$s_{t + 1} \in \mathcal{S}$$.

Now the goal of reinforcement learning is to learn *optimal* policies (or approximately optimal policies). So how do we define an optimal policy? A policy is optimal if its expected return is at least as large as any other policy in every state of the world. It's common to denote an optimal policy (which may not be unique) by $$\pi^{\ast}$$, and to denote the corresponding value and action-values functions by $$V^{\ast}$$ and $$Q^{\ast}$$. Note that whereas the optimal policy may not be unique, the optimal value function is.

Whatever the optimal policy is, its certainly a policy, and it must therefore also follow the Bellman equation above. However, the optimality of the policy allows to write this equation out slightly differently:

$$ V^{\ast}(s) = \max_a \mathbb{E}_{\pi^\ast} \left[ r_t + \gamma V^{\ast}(s_{t + 1}) \ \vert \ s_t = s, a_t = a \right] $$

In a sense, writing out the $$\max_a$$ doesn't really change this expression at all from what we wrote before because by definition following $$\pi^{\ast}$$ already implies that we take such an action, but the idea is to make it explicit that we can break the optimization into two parts: maximizing this periods reward plus the return to follow assuming that we act optimality in every subsequent period. The Bellman equation shows up time and again in deep reinforcement learning. In my next post, I'll take a look at the role it plays in some *classical* reinforcement learning algorithms.


## References
- [SB] Sutton & Barro,. [Reinforcement Learning: An Introduction](https://mitpress.mit.edu/books/reinforcement-learning-second-edition), (2018).
- [E] Evans. ["An Introduction to Mathematical Optimal Control theory"](https://math.berkeley.edu/~evans/control.course.pdf), (1983).
