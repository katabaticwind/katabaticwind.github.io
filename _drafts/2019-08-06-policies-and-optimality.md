---
layout: post
author: Colin Swaney
title: Reinforcement Learning Primer
date: 2019-06-01
categories: [research]
category: research
tags: [reinforcement learning]
excerpt: "<p>The first in a series of notes on deep reinforcement learning.</p>"
---



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
