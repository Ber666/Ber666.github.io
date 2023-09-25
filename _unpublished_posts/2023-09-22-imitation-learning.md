---
layout: post
title: "Revisiting RL in Foundation Model era (I: Imitation Learning)"
date: 2023-09-22 09:56:00-0400
description: Notes of Imitation Learning basics and recent papers
tags: "reinforcement-learning"
categories:
giscus_comments: false
related_posts: false
related_publications: 
---

## Imitation Learning



### What's the difference with off-policy RL?

https://bair.berkeley.edu/blog/2022/04/25/rl-or-bc/

Outcome-conditioned RL | explain Prompting?

## Recent Papers

### Decision Transformer

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