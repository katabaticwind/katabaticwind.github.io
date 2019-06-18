---
layout: post
title:  "ICML 2019: Highlights"
date: 2019-06-17 11:03
---

Last week I attended the International Conference on Machine Learning (ICML) in Long Beach, CA. ICML is one of the premier conferences on machine learning in the world, and is attended by luminaries in the field. Leaders of top research labs (e.g. Yann LeCun and David Silver) and representatives from industry (e.g. DeepMind, OpenAI, and Google Brain) give talks on recent advances. This was my first time attending ICML, and I didn't know exactly what to expect. I skipped the tutorials on Monday.

The conference began on Tuesday with an invited talk by John Abowd. John is an economist (possibly the only other economist by training at the conference other than myself) currently working U.S. Census Bureau. I went to the talk with the expectation that it would be boring because--well, it's the census. But in fact the bureau faces a challenging problem: how to provide data to users in a manner that is useful while still maintaining reasonable privacy of respondents? Essentially, the more statistics the bureau provides, the less anonymous the respondents become because it becomes easier to match respondents against commercially available datasets. To alleviate this problem, you can add randomness to the statistics (or the data used to generate statistics), which reduces the accuracy of the statistics obviously. None of this really means much to me, but it should be followed closely by people at Facebook, Google, and generally anywhere collecting large amounts of data about users.

In the afternoon I attended a series on time series that featured a number of interesting talks. Most of the talks in that section involved incorporating neural networks into time series models.

The following day featured an invited talk by Aude Billard from the École Polytechnique Fédérale de Lausanne in Switzerland. Dr. Billard is an expert in robotics. She demonstrated a number of experiments that her lab had performed using robotic arms. The main task was for the arm--which has a hand attached--to catch objects thrown in its direction. They started with simple objects (e.g. a ball), and moved up towards difficult items (e.g. a tennis racquet). The key connection to machine learning was in the way that the robot learned to predict the flight path on the object, and thereby determine its actions. Essentially, they use machine learning to approximate/estimate dynamical systems, which are the key to solving the control problem quickly. Another interesting problem her group is working on involves robots that learn to perform tasks that change the object they are manipulating. For example, a robot peeling a vegetable.

In the morning I went to a series of talks on deep reinforcement learning. The first talk was given by Natasha Jacques from DeepMind, who discussed her paper [Social Influence as Intrinsic Motivation for Multi-Agent Deep Reinforcement Learning](https://arxiv.org/abs/1810.08647). The other interesting paper from this section was [Structed Agents for Physical Construction](https://arxiv.org/abs/1904.03177), which was discussed by first author Jessica Hamrick, also of DeepMind. The talk demonstrated how "graph networks" can improve the ability of simulated robots to perform construction tasks such as stacking a set of blocks in a particular pattern. Graph networks look like an interesting idea: they generalize several existing neural network architectures.

Thursday morning featured an invited talk by cognitive psychologist and child cognition expert Alison Gopnik. Measured by audience engagement and questions after the talk, this was the most popular talk of the conference (but that is probably owing to the fact that it was the least technical, and therefore best understood). Dr. Gopnik talked about child cognition, constrasting how children learn and think with how adults learn and think, and suggested that researchers in the field of machine learning would do well to focus more on replicating the prior than the latter. One of the interesting points of her talk was the difference in the amount of time children and adults are willing to take exploring. Children are more likely to consider unlikely--but true--conclusions, whereas adults will more often dismiss unlikely hypotheses and persist with low-value, high-likelihood strategies. Kids engagement in a form of "structured" or "directed" exploration in which they explore systems in a seemingly random way, but which is infact limited and focused by human biology, allowing them to see how things really work. Clearly playing around with things is the efficient way to learn, but don't tell university professors that! This suggests an important habit: making time to try new things and explore new ideas **with no expectation of reward**. Just do something because it seems interesting and see what happens. Probably nothing, but its not a waste of time.

On Friday I jumped back-and-forth between the AI in Finance and Model-Based Reinforcement Learning workshops, both of which were quite interesting.

The AI in finance workshop (a.k.a "JPMorgan AI Workshop") largely focused on simulation and control of limit order books.

The model-based reinforcement learning workshop featured talks by David Silver (Value-Based Learning), Chelsea Finn (Compositional Reasoning), and Abhinav Gupta (Self-Supervised Learning).  