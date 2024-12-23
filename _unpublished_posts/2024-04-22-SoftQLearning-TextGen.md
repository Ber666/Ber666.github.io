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


## Background: Functional Derivative
A functional $J(q)$ is a mapping from function $q(x)$ to a real value. Here, we focus on a simple case of functional, which can be expressed as an integration of a function of $q(x)$:

$$ J[q] = \int_a^b F(q(x), x) \, dx $$

**Definition**

To measure "how a small change in $q(x)$ would affect $J(q)$", we define the functinoal derivative $\frac{\delta J}{\delta q(x)}$ as follows:


$$
 J[q + \epsilon \eta] - J[q] \approx \epsilon \int \frac{\delta J}{\delta q(x)} \eta(x) \, dx 

$$
Here, $\eta(x)$ is an arbitrary test function, similar to the role of $\Delta x$ in function derivative.

**Solution**

It's easy to calculate the functional derivative of $J$ in the simple form defined above. First, adding some perturbation to $J$:

$$ J[q + \epsilon \eta] = \int_a^b F(q(x) + \epsilon \eta(x),  x) \, dx $$

Applying Taylor expansion to $F$ around $x$: 


$$ J[q + \epsilon \eta] = \int_a^b \left[F(q(x),  x) \, + \frac{\partial F}{\partial q} \epsilon \eta(x)\right] dx $$


$$ J[q + \epsilon \eta] - J[q] = \epsilon \int_a^b \frac{\partial F}{\partial q} \eta(x) dx $$

Looking back at the definition of functional derivative, we'll find that $\frac{\delta J}{\delta q(x)}=\frac{\partial F}{\partial q}$

**Application**

The conclusion above can be applied to solve the problem of entropy-regularized expectation optimization:
$$

 \mathcal{F}(q) = \int q(x) f(x) \, dx + \lambda \int q(x) \log q(x) \, dx 
$$

The functional derivative of $ \mathcal{F}(q) $ with respect to $ q(x) $ involves:

- **Expectation Term**:
   $$ \frac{\delta}{\delta q(x)} \left( \int q(x) f(x) \, dx \right) = f(x) $$

- **Entropy Term**:
   $$ \frac{\delta}{\delta q(x)} \left( \lambda \int q(x) \log q(x) \, dx \right) = \lambda (\log q(x) + 1) $$

Combining these:

$$ \frac{\delta \mathcal{F}(q)}{\delta q(x)} = f(x) + \lambda (\log q(x) + 1) $$

Setting this to zero to find the optimal $ q(x)$:

$$ f(x) + \lambda (\log q(x) + 1) = 0 $$

Solving for $q(x)$:

$$ \log q(x) = -\frac{f(x)}{\lambda} - 1 $$
$$ q(x) = \exp\left( -\frac{f(x)}{\lambda} - 1 \right) $$

Since we haven't put any constraint to $q$, the resulting $q$ may not be a probability distribution. Fortunately, we know that adding any constant $C$ to $f(x)$ will not affect the solution $q(x)$, and there must exist a $C$ that makes the solution $q(x)$ integrate to $1$. Then, the solution $q(x)$ will satisfy:

$$ q(x) \propto \exp\left( -\frac{f(x)}{\lambda} \right) $$

## Formulation

The objective of maximum entropy RL is:

$$
\pi_{\text {MaxEnt }}^*=\arg \max _\pi \sum_t \mathbb{E}_{\left(\mathbf{s}_t, \mathbf{a}_t\right) \sim \rho_\pi}\left[r\left(\mathbf{s}_t, \mathbf{a}_t\right)+\alpha \mathcal{H}\left(\pi\left(\cdot \mid \mathbf{s}_t\right)\right)\right]
$$

Note this objective not only maximizes entropy **at the current time step**, but also encourages the policy to reach states where they will have high entropy **in the future**.

We can first define soft Q-function and soft value function by incorporating entropy into standard Q-function and value function:

$$
\begin{aligned}
& Q_{\text {soft }}^{\pi}\left(\mathbf{s}_t, \mathbf{a}_t\right)=r_t+ \mathbb{E}_{\left(\mathbf{s}_{t+1}, \ldots\right) \sim \rho_\pi}\left[\sum_{l=1}^{\infty} \gamma^l\left(r_{t+l}+\alpha \mathcal{H}\left(\pi\left(\cdot \mid \mathbf{s}_{t+l}\right)\right)\right)\right]
\end{aligned}
$$

$$
V_{\mathrm{soft}}^\pi\left(\mathbf{s}_t\right)=\mathbb{E}_{a'\sim \pi(\mathbf{s}_t)} \left[Q_{\mathrm{soft}}^\pi\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right) + \alpha \mathcal{H}\left(\pi\left(\cdot \mid \mathbf{s}_{t+l}\right)\right)\right]
$$

(This definition of soft value function has been shortened by using $Q$.)

To improve a policy $\pi$, we can optimize the soft value function above, which is $\hat\pi(\mathbf{s}_t)\propto \exp\left(\frac{1}{\alpha}Q_{\mathrm{soft}}^\pi\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right)$. That's what we have proved with functional derivative.

The optimal soft policy has to satisfy $\pi^*(\mathbf{s}_t)\propto \exp\left(\frac{1}{\alpha}Q_{\mathrm{soft}}^*\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right)$, because otherwise it can be further improved in this way. We can directly plug it into the definition of soft value function, and get soft Bellman equation (Theorem 2 in SQL):
$$
V_{\mathrm{soft}}^*\left(\mathbf{s}_t\right)=\alpha \log \int_{\mathcal{A}} \exp \left(\frac{1}{\alpha} Q_{\mathrm{soft}}^*\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right) d \mathbf{a}^{\prime}
$$

When $\alpha=0$, it becomes standard Bellman equation, where the $\log \int\exp$ operator becomes $\max$. 

The integration in the equation above is the normalizing constant of policy, so we can express the constant with soft value function: $\int_{\mathcal{A}} \exp \left(\frac{1}{\alpha} Q_{\mathrm{soft}}^*\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right) d \mathbf{a}^{\prime}=\exp \frac{1}{\alpha}V_{\mathrm{soft}}^*\left(\mathbf{s}_t\right)$. Then, the optimal soft policy can be written as below (Theorem 1 in SQL):

$$
\pi_{\text {MaxEnt }}^*\left(\mathbf{a}_t \mid \mathbf{s}_t\right)=\exp \left(\frac{1}{\alpha}\left(Q_{\mathrm{soft}}^*\left(\mathbf{s}_t, \mathbf{a}_t\right)-V_{\mathrm{soft}}^*\left(\mathbf{s}_t\right)\right)\right)
$$

## Soft Q-Learning

Soft Q-Learning is based on fixed-point iteration that resembles Q-iteration.

$$
\begin{aligned}
Q_{\text {soft }}\left(\mathbf{s}_t, \mathbf{a}_t\right) & \leftarrow r_t+\gamma \mathbb{E}_{\mathbf{s}_{t+1} \sim p_{\mathbf{s}}}\left[V_{\text {soft }}\left(\mathbf{s}_{t+1}\right)\right], \forall \mathbf{s}_t, \mathbf{a}_t \\
V_{\text {soft }}\left(\mathbf{s}_t\right) & \leftarrow \alpha \log \int_{\mathcal{A}} \exp \left(\frac{1}{\alpha} Q_{\text {soft }}\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right) d \mathbf{a}^{\prime}, \forall \mathbf{s}_t
\end{aligned}
$$

The iteration will converge to $Q^*_{\text{soft}}$ and $V^*_{\text{soft}}$ (Theorem 3). Apparently, the policy will be monotonically improved in this way, and will only stop when the soft Bellman equation is satisfied everywhere.

### Learning soft Q function

Soft Q Learning parametrize soft Q function $Q_{\text{soft}}^\theta(s,a)$ and represent soft value function with it. At each step of soft Q-iteration: 

- First estimate $V^\theta_{\text{soft}}(s)$ with importance sampling using any policy $q$:
   $$
   V_{\mathrm{soft}}^\theta\left(\mathbf{s}_t\right)=\alpha \log \mathbb{E}_{q_{\mathbf{a}^{\prime}}}\left[\frac{\exp \left(\frac{1}{\alpha} Q_{\mathrm{soft}}^\theta\left(\mathbf{s}_t, \mathbf{a}^{\prime}\right)\right)}{q_{\mathbf{a}^{\prime}}\left(\mathbf{a}^{\prime}\right)}\right]
   $$

- Then we could calculate the **target Q function** using the value function of next states.
   $$
   \hat{Q}_{\mathrm{soft}}^{\bar{\theta}}\left(\mathbf{s}_t, \mathbf{a}_t\right)=r_t+\gamma \mathbb{E}_{\mathbf{s}_{t+1} \sim p_{\mathrm{s}}}\left[V_{\mathrm{soft}}^{\bar{\theta}}\left(\mathbf{s}_{t+1}\right)\right]
   $$

- Optimize the soft Q function towards the target Q function:
   $$
   J_Q(\theta)=\mathbb{E}_{\mathbf{s}_t \sim q_{s_t}, \mathbf{a}_t \sim q_{a_t}}\left[\frac{1}{2}\left(\hat{Q}_{\text {soft }}^{\bar{\theta}}\left(\mathbf{s}_t, \mathbf{a}_t\right)-Q_{\text {soft }}^\theta\left(\mathbf{s}_t, \mathbf{a}_t\right)\right)^2\right]
   $$


### Sampling from soft policy

The policy derived from soft Q function is an engergy-based model.

Finally, the paper introduces SVGD to sample from the policy, which is an energy-based model of soft Q function.

The paper mainly discussed continous action space. For discrete space, there is no need to use SVGD to sample from the policy. Besides, we won't need importance sampling to estimate soft value function from the soft Q-function.




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