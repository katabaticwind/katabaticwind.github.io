---
layout: post
title:  "ICML 2019: Highlights"
date: 2019-06-17 11:03
---

Last week I attended the International Conference on Machine Learning (ICML) in Long Beach, CA. ICML is one of the most important conferences on machine learning in the world, attracting paper submissions and hosting invited talks by top researchers in the field, such as Yann LeCun and David Silver, as well as small armies of researchers from industry outfits such as DeepMind, OpenAI, and Google Brain.

## Tuesday, June 11

### Invited Talk: John Abowd
This was my first time attending ICML, and I didn't know exactly what to expect. There was an optional tutorial session on Monday, but I decided to skip this and attend the workshops at the end of the week instead. The conference officially kicked off on Tuesday with an invited talk by John Abowd. John is an economist (possibly the only other economist by training at the conference other than myself) who is currently the U.S. Census Bureau's associate director of research and methodology. I went to the talk with the expectation that it would be boring because, well, it's the Census Bureau. But in fact the bureau faces a challenging problem, which is figuring out how to provide data (statistics) to users in useful while maintaining the privacy of its respondents. It turns out that there is a *fundamental* trade-off between information and privacy: if you publish too many statistics too accurately, then no one has any privacy (and, conversely, perfect privacy implies *worthless* data). John calls the "Fundamental Law of Information Recovery".

Essentially, the more statistics the bureau provides, the easier it is to match respondents against commercially available datasets (a "[reconstruction attack](https://queue.acm.org/detail.cfm?id=3295691)"). To alleviate this problem, the Census Bureau adds randomness to the statistics it provides (or to the data used to generate statistics), which obviously reduces the accuracy of the statistics. This process is known as [differential privacy](https://en.wikipedia.org/wiki/Differential_privacy), and John and his team have done a lot of great work putting this concept into practice at the Census Bureau, but at the end of the day someone will still need to decide how to balance privacy against accuracy--a task for which there is no guideline or mandate. None of this really means much to me, but it seems like an interesting line of research, and it is certainly important to Facebook, Google, and generally any group collecting large amounts of data about private data. If you're interested, this [paper](https://arxiv.org/abs/1809.02201) describes some of the challenges John and his colleagues have encountered incorporating differential privacy at the Census Bureau. (If you're *really* interested, the complete 1940 census records have been available since 2012 if you want to play around with this stuff—you can find the data [here](https://1940census.archives.gov/index.asp)).

### Morning Session: Deep Reinforcement Learning


### Afternoon Session: Time Series Analysis
In the afternoon I attended a series on time series that featured a number of interesting talks. Most of the talks in that section involved incorporating neural networks into time series models.


## Wednesday, June 12

### Invited Talk: Aude Billard
The following day featured an invited talk by Aude Billard entitled "Machine Learning for Robots to Think Fast". Dr. Billard is the head of the [LASA laboratory](http://lasa.epfl.ch) at the École Polytechnique Fédérale de Lausanne in Switzerland. She is one of the world's leading experts in robotics. Her talk demonstrated a series of tasks that her lab had "trained" robotic arms to perform. In one task, a single arm—which includes a dexterous hand—tries to [catch objects](https://www.youtube.com/watch?v=JEob5Slkvjw) thrown in its direction. The researchers started by throwing objects that are simple to catch, like a small ball, and then moved on to more difficult items such as a tennis racquet or a half-full (or was it half-empty?) bottle of water. With the simple objects, the flight path is easier to predict, but the robot still needs to make a quick calculation to determine how to move its hand to meet the object. Catching a half-full bottle of water is more challenging because its path is irregular, and the robot may need to make a last-minute adjustment to its hand position to catch the bottle.

There seem to be two keys to the robots success in this task. First, the researchers use direct demonstrations to teach the robot, that is, they throw the object and manually move the robot's arm into position so that it experiences an approximately correct motion. Second, they combine physical modeling (dynamical systems) with machine learning techniques (e.g., Gaussian mixture models, support vector machines, and nonlinear/extended Kalman filters) to learn a model of the world. It's a rather elegant—and clearly effective—combination of machine learning and applied mathematics, and a superb example of how machine learning compliments traditional science, especially in practical applications. (If this is sounds interesting, slides and code from a tutorial her lab recently led can be found [here](https://epfl-lasa.github.io/TutorialICRA2019.io/)).

In another task, a pair of robotic arms learn to [peel vegetables](https://www.youtube.com/watch?v=xIK6U52TjRM), which requires coordination between the arms as well as adaptation to the changes to the vegetable as it is peeled. At the end of the talk, Dr. Billard was asked what the biggest challenge in robotics is, and her answer was "general object manipulation", and in particular the ability to handle objects and perform tasks that change the object's shape.

### Morning Session: Deep Reinforcement Learning
In the morning I went to a series of talks on deep reinforcement learning. The first talk was given by Natasha Jacques from DeepMind, who discussed her paper [Social Influence as Intrinsic Motivation for Multi-Agent Deep Reinforcement Learning](https://arxiv.org/abs/1810.08647). The other interesting paper from this section was [Structed Agents for Physical Construction](https://arxiv.org/abs/1904.03177), which was discussed by first author Jessica Hamrick, also of DeepMind. The talk demonstrated how "graph networks" can improve the ability of simulated robots to perform construction tasks such as stacking a set of blocks in a particular pattern. Graph networks look like an interesting idea: they generalize several existing neural network architectures.

### Afternoon Session: Break

## Thursday, June 13

### Invited Talk: Alison Gopnik
Thursday morning featured an invited talk by cognitive psychologist and child cognition expert Alison Gopnik. Measured by audience engagement and questions after the talk, this was the most popular talk of the conference (but that is probably owing to the fact that it was the least technical, and therefore best understood). Dr. Gopnik talked about child cognition, constrasting how children learn and think with how adults learn and think, and suggested that researchers in the field of machine learning would do well to focus more on replicating the prior than the latter. One of the interesting points of her talk was the difference in the amount of time children and adults are willing to take exploring. Children are more likely to consider unlikely--but true--conclusions, whereas adults will more often dismiss unlikely hypotheses and persist with low-value, high-likelihood strategies. Kids engagement in a form of "structured" or "directed" exploration in which they explore systems in a seemingly random way, but which is infact limited and focused by human biology, allowing them to see how things really work. Clearly playing around with things is the efficient way to learn, but don't tell university professors that! This suggests an important habit: making time to try new things and explore new ideas **with no expectation of reward**. Just do something because it seems interesting and see what happens. Probably nothing, but its not a waste of time.

## Friday, June 14

### Morning Workshop: AI in Finance
On Friday I jumped back-and-forth between the AI in Finance and Model-Based Reinforcement Learning workshops, both of which were quite interesting.

The AI in finance workshop (a.k.a "JPMorgan AI Workshop") largely focused on simulation and control of limit order books.

### Afternoon Workshop: Model-Based Reinforcement Learning
The model-based reinforcement learning workshop featured talks by David Silver (Value-Based Learning), Chelsea Finn (Compositional Reasoning), and Abhinav Gupta (Self-Supervised Learning).  
