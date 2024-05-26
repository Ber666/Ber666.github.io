---
layout: post
title: "Soft Q-learning and Text Generation"
date: 2024-04-22 09:56:00-0400
description: "Looking into the connection between text generation and RL"
tags: "RL, LLM"
categories:
giscus_comments: false
related_posts: false
related_publications: 
---

- Nachum et al., 2017, "Bridging the Gap Between Value and Policy Based Reinforcement Learning"
- Haarnoja et al., 2017, "Reinforcement Learning with Deep Energy-Based Policies"

- "Preference Fine-Tuning of LLMs Should Leverage Suboptimal, On-Policy Data"
- Chelsea Finn
- Dense Reward for Free in Reinforcement Learning from Human Feedback

- a new **connection between value and policy based RL**.

- Specifically, we show that softmax consistent action values correspond to optimal entropy regularized policy probabilities along any action sequence, regardless of provenance.

- A new RL algorithm, Path Consistency Learning (PCL)

- PCL can be interpreted as generalizing both actor-critic and Q-learning algorithms.

- A single model can be used to represent both a policy and the corresponding softmax state values

## Max Entropy RL

Q-Learning:

Bell

$$V^\pi (s)=\sum_a \pi(a|s) [r(s, a) + \gamma V^\pi (s') ]\text{ where } s'=f(s, a)$$

Hard-max Bellman temporal consistency:

$$V^*(s)=\max_a [r(s, a) + \gamma V^* (s') ]\text{ where } s'=f(s, a)$$

Or equivalently,

$$Q^*(s, a)=r(s, a) + \gamma \max_a Q^*(s, a)$$

Q-learning relies on a value iteration algorithm based on the equation above.

## Softmax Temporal Consistency

What if we augment the standard expected reward objective with a discounted entropy regularizer?

Define the discounted entropy 
$$\mathbb H^{\pi}(s) = \sum_a \pi (a\mid s)[-\log \pi(a\mid s) + \gamma \mathbb{H}^\pi (s')$$ 

If we add $\tau\mathbb{H}^\pi (s_0)$ to the objective function (in addition to accumulated reward), the new value function becomes

$$V^\pi (s) = \sum_a \pi(a\mid s)[r(s,a) - \tau \log \pi(a\mid s) + \gamma V^\pi (s') ]$$

Now the optimal policy is not a one-hot distribution any more. It can be proved that:

$$\pi^*(a \mid s) \propto \exp \left\{\left(r(s, a)+\gamma V^*\left(s^{\prime}\right)\right) / \tau\right\}$$

> Functional derivative
> https://podcast.ucsd.edu/watch/wi23/dsc291_e00/2 (44:07)


(need derivation)

So, we have the softmax version of Bellman equation.

$$V^*(s)=\tau \log \sum_a \exp \left\{\left(r(s, a)+\gamma V^*\left(s^{\prime}\right)\right) / \tau\right\}$$

$$Q^*(s, a)=r(s, a)+\gamma \tau \log \sum_{a^{\prime}} \exp \left(Q^*\left(s^{\prime}, a^{\prime}\right) / \tau\right)$$

Using value-iteration similar to Q-learning, it converges to the optimal values. (Appendix C)

Theorem 1: 

$$V^*(s)-\gamma V^*\left(s^{\prime}\right)=r(s, a)-\tau \log \pi^*(a \mid s)$$

or, equivalently,

$$\pi^*(a \mid s)=\exp \left\{\left(Q^*(s, a)-V^*(s)\right) / \tau\right\}$$


Corollary 2:

$$V^*\left(s_1\right)-\gamma^{t-1} V^*\left(s_t\right)=\sum_{i=1}^{t-1} \gamma^{i-1}\left[r\left(s_i, a_i\right)-\tau \log \pi^*\left(a_i \mid s_i\right)\right]$$



Theorem 3:

If a policy $π(a\mid s)$ and state value function $V (s)$ satisfy the consistency property (in Theorem 1) for all states $s$ and actions $a$, then $π = π^*$ and $V = V^*$

Algorithm 1:

Path Consistency Learning (PCL)

$$C\left(s_{i: i+d}, \theta, \phi\right)=-V_\phi\left(s_i\right)+\gamma^d V_\phi\left(s_{i+d}\right)+\sum_{j=0}^{d-1} \gamma^j\left[r\left(s_{i+j}, a_{i+j}\right)-\tau \log \pi_\theta\left(a_{i+j} \mid s_{i+j}\right)\right]$$

$$\mathcal{L}(\theta, \phi) = \sum_{s_{i:i+1}\in E}\frac{1}{2}C(s_{i:i+d}, \theta, \phi)^2$$

If we unify the value model and policy model... We can simply parametrize $Q_\rho$, and represent $V$ and $\pi$.

Algorithm 2:

Unified PCL

$$\begin{aligned}
V_\rho(s) & =\tau \log \sum_a \exp \left\{Q_\rho(s, a) / \tau\right\} \\
\pi_\rho(a \mid s) & =\exp \left\{\left(Q_\rho(s, a)-V_\rho(s)\right) / \tau\right\}
\end{aligned}$$

**Merging the policy and value function models in this way is significant because it presents a new actor-critic paradigm where the policy (actor) is not distinct from the values (critic)**

## Relation to other RL algorithms





# Soft Q-learning
Soft Q-learning

Amortized Stein variational gradient descent

This reveals an intriguing connection: entropy regularized actorcritic algorithms can be viewed as approximate Q-learning methods, with the actor serving the role of an approximate sampler from an intractable posterior.

**improved exploration**

**generalization** "provide a degree of compositionality in reinforcement learning by showing that stochastic energy-based policies can serve as a much better initialization for learning new skills"

Note that this objective differs qualitatively from the behavior of Boltzmann exploration (Sallans & Hinton, 2004) and PGQ (O’Donoghue et al., 2016),
which greedily maximize entropy at the current time step,
but do not explicitly optimize for policies that aim to reach
states where they will have high entropy in the future. 


Theorem 1
$$\pi_{\text {MaxEnt }}^*\left(\mathbf{a}_t \mid \mathbf{s}_t\right)=\exp \left(\frac{1}{\alpha}\left(Q_{\text {soft }}^*\left(\mathbf{s}_t, \mathbf{a}_t\right)-V_{\text {soft }}^*\left(\mathbf{s}_t\right)\right)\right)$$

**This is similar to the Theorem 1 in the last paper?**
The functional derivative part?

Theorem 2 (trivial)

## Soft Actor-Critic

Unfortunately, the combination of off-policy learning and high-dimensional, nonlinear
function approximation with neural networks presents a major challenge for stability and convergence.

This challenge is further exacerbated in continuous state and action spaces, where a separate actor network is often used to perform the maximization in Q-learning.

SAC compared to AC -> Offline
SAC compared to Q-learning -> Easier to converge / continuous