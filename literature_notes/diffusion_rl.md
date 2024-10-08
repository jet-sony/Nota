# Diffusion Reinforcement Learning

## What is diffusion? [$_1$](https://arxiv.org/abs/2105.05233)

We define the target distribution as a chain of denoising processes:

$$
  p_\theta(x_0) = \Pi_{t=1}^T p_\theta(x_{t-1} | x_t)
$$
where $p$ is the distribution of the data point, $x_0$ is the data point itself.

The forward process is defined as a process of continually noising the original datapoint:

$$
  q(x_T) = \Pi_{t=1}^T q(x_t | x_{t-1})
$$
where $q$ is the noising process

![.images/fcd5fa20-4c5f-450d-bd0e-ddb1010e83fc.png](.images/fcd5fa20-4c5f-450d-bd0e-ddb1010e83fc.png)

**Why does this matter?**

- Because you're not constrained by distributions that limit representation power:
  - VAE, VQVAE
- Autoregressive in manifold space, not output space.

**How does this compare to other generative models? [Lilian Weng](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/)**

![.images/cda3cde1-3d25-4bb9-83a9-a25f41501aaa.png](.images/cda3cde1-3d25-4bb9-83a9-a25f41501aaa.png)

---

### Some Math 

**Hyperparameters**

$$
  \alpha_t = 1 - \beta_t \\
  \bar{\alpha}_t = \Pi_{s=1}^t \alpha_t
$$
and therefore 
$$
  \alpha_t = \bar{\alpha}_t / \bar{\alpha}_{t+1}
$$
The most common way to schedule $\alpha_t$ is to use:
$$
\bar{\alpha}_t =  \cos(\frac{\pi t}{2 T})
$$

**Forward diffusion** (noising) process is formally defined as:

$$ 
  q(x_t | x_{t-1}) = \mathcal{N} (\sqrt{\alpha_t} x_{t-1}, (1 - \alpha_t) I)
$$
ie: scale down original image, add noise.

We can generalize this to n-step forward diffusion:
$$
  q(x_t | x_0) = \mathcal{N} (\sqrt{\bar{\alpha}_t} x_0, \sqrt{1 - \bar{\alpha}_t} I)
$$

**Reverse diffusion** process is formally defined as:

$$
  p_\theta (x_{t-1} | x_t) = \mathcal{N} (\mu(x_t, t), \sigma (x_t, t))
$$
where the mean is the base noise minus a predicted noise:
$$
  \mu(x_t, t) = \frac{1}{\alpha_t} \left(
    x_t - \frac{1 - \alpha_t}{\sqrt{1 - \bar{\alpha}_i}} \mu_\theta(x_t, t) 
  \right)
$$
and variance is a scaled variance:
$$
  \sigma(x_t, t) = (1 - \alpha) \mathbf{I}
$$
or in the learned variance case:
$$
  \sigma(x_t, t) = \sigma_\theta(x_t, t)
$$ 

**The optimization objective** is to generally optimize the ELBO:
$$
  \mathbb{E}_q \left[ 
    log \frac{p_\theta (x_{0:T})}{q_T}
p_\theta (x_{0:T}
) 
  \right]
$$
Which just boils down to:
$$
  \mathcal{L}_d = \mathbb{E}_{t \sim \mathcal{U}, \epsilon \sim \mathcal{N}(0, I), (x_0) \sim \mathcal{D}}
    \left[ 
      || \epsilon - \epsilon_\theta (\sqrt{\bar{\alpha}_t} x_t + \sqrt{1 - \bar{\alpha}_t} \epsilon, t) || ^ 2
    \right]
$$
where:
$$
  \epsilon_\theta(x_t, t) \sim \mathcal{N} (\mu_\theta (x_t, t), \sigma_\theta(x_t, t) )
$$
ie: sample data, apply random noise schedule, predict noise.

**During sampling**, we first sample $x_T \sim \mathcal{N} (0, I)$, then do:
$$
  x_{t-1} | x_t = \frac{x_t}{\sqrt{\alpha_t}} - \frac{1 - \alpha_t}{\sqrt{\alpha_t (1 - \bar{\alpha}_t)}} \epsilon_\theta (x_t, t) \\
$$

---

### Extension to Conditional Diffusion

Conditional diffusion is just diffusion, but instead of passing only $t$ to the denoising model, we also pass in a context vector.

---
---
---

## Diffusion Policies - Diffusion for Behaviour Cloning [$_1$](https://arxiv.org/pdf/2303.04137v4)

### Why is BC hard?

- Multimodal action distributions
- Distribution shift in low data regimes
- Idle actions - during the start of demonstrations, there may be timesteps where no actions are taken. 1 step BC tends to overfit to this and careful clipping of trajectories must happen.

### How does it work?

- Predict a sequence of actions, $x_t = (a_0, a_1, a_2, ..., a_n)$.
  - Paper uses $n = 16$.
- Condition on visual observation.
  - Paper uses embedding vector from ResNet19, 76x76 image or 288x216 image.
- Use transformer to condition on history.
  - Paper uses observation horizon = 2, action horizon = 8.
 
### What are the benefits?

- High dimension output space - we can now predict actions for many steps into the future
![.images/8eeedb60-f748-41c6-a5b3-92946e189f3f.png](.images/8eeedb60-f748-41c6-a5b3-92946e189f3f.png)
- Explainability - we can see what the policy is about to do
- Stable training - not sure if this is a perk because behaviour cloning already uses static distributions
- Can use direct position control - leverage the power of low level optimal control for robot positioning and RL for high level control

### Experiments

- Franka Kitchen, 566 demonstrations across 4 tasks
- Sauce pouring, 50 demonstrations


### Results

![.images/c110a213-ae6b-497f-9c68-db9e18de7432.png](.images/c110a213-ae6b-497f-9c68-db9e18de7432.png)
![.images/c107c074-8e1b-449b-82c8-bf834fd13186.png](.images/c107c074-8e1b-449b-82c8-bf834fd13186.png)

### Limitations

Offline RL > BC

---
---
---

## Classifier Guided Diffusion [$_1$](https://arxiv.org/abs/2105.05233)

What if we wanted to guide diffusion toward particular distributions within the training data during inference?
This would work to a limited extent for conditional diffusion, but won't give _complete flexibility_.

Assuming we have a classifier model that can give us gradients (such as CLIP), we can perform **classifier guided diffusion** instead.
Essentially, we modify the sampling step to be:

$$
  x_{t-1} = denoise(x_t) + \lambda \nabla_{x_i} \log f_\phi (c | x_t) 
$$
where $f_\phi$ is a categorial classifier and $c$ is the class category.
Ie: we use the gradients of the classification model to nudge the diffusion process.

### Advantages

- Class Control
- High quality samples

### Disadvantages

- Expensive to compute - one full backward pass per step
- Less creativity

## Classifier Free Diffusion [$_1$](https://arxiv.org/abs/2207.12598)

All big and popular image generation models use this.
Basically, we condition the diffusion model on an embedding vector (usually obtained from CLIP), and then alternate between with-embedding and without-embedding steps.

---
---
---

## Q-value guided off-policy diffusion RL

Off-policy RL's goal is to learn a policy that maximizes the Q values from trajectories within an offline dataset.
To accomplish this, you can inject the Q value into the learning policy in the same manner as CFD or CGD:

> Assume that $x$ = $a$ for notational convention

### Option 1: [direct endpoint optimization](https://arxiv.org/pdf/2208.06193) (Diff QL)

$$
  \mathcal{L}(\theta) = \mathcal{L}_d - k \cdot \mathbb{E}_{\mathbf{s} \sim \mathcal{D}, a \sim \mathcal{\pi_\theta}} [Q(s, a)]
$$

Downsides: must differentiate through entire chain.

![.images/49e27853-467d-46ea-9223-fce28d176138.png](.images/49e27853-467d-46ea-9223-fce28d176138.png)

### Option 2: [one-step approximation](https://proceedings.neurips.cc/paper_files/paper/2023/file/d45e0bfb5a39477d56b55c0824200008-Paper-Conference.pdf) (EDP)

Recall that the noised state can be represented as:
$$
  a_t = \mathcal{N} (\sqrt{\bar{\alpha}_t} a_0, \sqrt{1 - \bar{\alpha}_t} I)
$$
Therefore, we can also approximate the result of reverse diffusion using one-step approximation:
$$
  \pi_\theta(\hat{a}_0) = \frac{a_t}{\sqrt{\bar{\alpha}_t}} - \frac{\sqrt{1 - \bar{\alpha}_t}}{\sqrt{\bar{\alpha}_k}} \epsilon_\theta (a_t, t)
$$
And then use that to directly optimize the Q value.

This form can be generalized to other off-policy algorithms, such as IQL, AWR, CRR, using the following (after derivations):
$$
  \mathcal{L}(\theta) = f(s, a, \hat{a}_0) || a - \hat{a}_0 || ^ 2
$$
where $a$ is the action sampled from the dataset, and $\hat{a}_0$ is the policy approximated action.

![.images/e1789ef5-b4fb-4c26-8cad-1a770bd8861b.png](.images/e1789ef5-b4fb-4c26-8cad-1a770bd8861b.png)

![.images/7d836be5-17e0-4b17-9407-dcbeeade4282.png](.images/7d836be5-17e0-4b17-9407-dcbeeade4282.png)



