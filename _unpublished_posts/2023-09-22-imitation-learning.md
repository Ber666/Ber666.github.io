---
layout: post
title: "Revisiting RL in the LLM era (I. Introduction and Imitation Learning)"
date: 2023-09-22 09:56:00-0400
description: Notes of Imitation Learning basics and recent papers
tags: "reinforcement-learning"
categories:
giscus_comments: false
related_posts: false
related_publications: 
---


Sequence modeling and RL


A year ago, ChatGPT sparked the trend of large language models (LLMs), and its immense capabilities motivated people to seriously consider the future of **autonomous agents**. Though seemingly there is not consensus on what this term means, I believe it may include the following aspects: Acting (an active and relevant research topic is tool learning), planning, and learning from interactions... It's a pity that most of the current research on agents seems to focus on the low-hanging fruit of simply manipulating prompts / finetune LLMs with data distilled from GPT-4. However, I believe this direction holds unlimited potential and could be the foundation for the next generation of foundation models, generating more influential real-world applications even than ChatGPT. And, I do think progress in agents may require fundamental methodological innovations.

In fact, the research goals of agents align closely with the vision of the **reinforcement learning (RL)** community, but research on LLMs and RL has been relatively disjointed. Before the hype of ChatGPT and RLHF entered the scene, the mainstream view was that RL had never really worked in NLP. This shift may have come from the scaling up of models, as the basic capabilities of models increased, giving RL a chance to make an impact. I believe that in the not-so-distant future, Reinforcement Learning technologies will be a treasure trove for the development of agents.

Another reason why I'm particularly fascinated by RL is that they consistently encapsulate intuition into a concise and unified formulation, while NLP researchers sometimes seek ad-hoc solutions ignoring principles. In my opinion, the best research always comes from a combination of intuition, principles, and empirical results. Overall, based on my personal interests and historical trends, I feel that dedicating more time to RL and approaching problems from this perspective is worthwhile. That motived me to organize what I learn and think by writing this blog series.

## Behavior Cloning

This is the simplest algorithm of imitation learning. It's simply 



Distribution Shift (no assumption of deterministic dynamics)

Exposure bias (NLP)

Paper: Why Exposure Bias Matters: An Imitation Learning Perspective of Error Accumulation in Language Generation

### What's the difference with off-policy RL?

- Offline RL: Given a fixed dataset of trajectories $\mathcal{D}=\{(s_i, a_i, s'_i, r_i)\}$, learn the best possible policy $\pi_\theta$.
- 
- For a more systematic review, please refer to the [blog](https://bair.berkeley.edu/blog/2022/04/25/rl-or-bc/) from BAIR and the corresponding paper: [When Should We Prefer Offline Reinforcement Learning Over Behavioral Cloning?]((https://arxiv.org/abs/2204.05618))

Outcome-conditioned RL | explain Prompting?

temporal compositionality

How would a typo in prompt matter?

Train on some toy synthetic data

## Recent Papers

### Decision Transformer

- [video](https://www.youtube.com/watch?v=w4Bw8WYL8Ps)
- Motivation: Large Transformer Modals are **stable** to train and scale up.
- Need to set initial returns-to-go to desired target return (expert)
- inference: rollouts
- Similar but still stronger than %imitation learning (by learning from bad trajectory)
- What's the relation to DPO?

### RT-1

- Motivation: Foundation model for robotic control
- Architecture:
  - RT-1 takes a short sequence of images and a task description as input, and outputs an action to execute at each time step.
  - A pretrained text embedding model **FiLM** to encode the task description, and the ImageNet-pretrained **EfficientNet** to encode the images conditioned on text embedding. A **Token Learner** module to compute a compact set of tokens, and finally a Transformer to attend over these tokens and produce discretized action tokens.
  - Discretizing each dimension of the action space into bins to model multi-modal distribution.  
  ![Alt text](2023-09-22-imitation-learning.assets\image-rt1.png)
- Data
- Inference
  - RT-1 performs closed-loop control and commands actions at 3 Hz until it either yields a “terminate” action or hits a pre-set time step limit.
  - Generaliztion
  - Combined with SayCan for long-horizon tasks
  - Other ablation


https://youtu.be/UuKAp9a6wMs





Can we do trajectory stitching without learning a Q function? 

Dataset - return | generalization?

![image-20230925100730599](2023-09-22-imitation-learning.assets/image-20230925100730599.png)

Decision Transformers

Trajectory Transformers	

 

Composition of constraints