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

**Forward diffusion** (noising) process is formally defined as:

$$
  q(x | x_{t-1}) = \mathcal{N} (\sqrt{1 - \beta_t} x_{t-1}, \beta_t I)
$$
ie: scale down original image, add noise.

**Reverse diffusion** process is formally defined as:

$$
  p_\theta (x_{t-1} | x_t) = \mathcal{N} (\mu_\theta (x_t, t), \Sigma_\theta (x_t, t))
$$
where the mean predictor is a predictor for just the noise:
$$
  \mu_\theta (x_t, t) = \frac{1}{\alpha_t} \left(
    x_t - \frac{\beta_t}{\sqrt{1 - \alpha_i}} \epsilon_\theta(x_t, t) 
  \right)
$$
ie: we predict the noise scaled and offset by some scheduler.

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
  \mathcal{L} = \mathbb{E}_{t \sim \mathcal{U}, \epsilon \sim \mathcal{N}(0, I), (x_0) \sim \mathcal{D}}
    \left[ 
      || \epsilon - \epsilon_\theta (\sqrt\alpha_t x_t + \sqrt{1 - \alpha_t} \epsilon, x_t, t) || ^ 2
    \right]
$$
ie: sample data, apply random noise schedule, predict noise.

**During sampling**, we first sample $x_T \sim \mathcal{N} (0, I)$, then do:
$$
  x_{t-1} | x_t = \frac{x_t}{\sqrt{\alpha_t}} - \frac{\beta_t}{\sqrt{\alpha_t (1 - \alpha_t)}} \epsilon_\theta (x_t, t) + \sqrt{\beta_t} \epsilon\\
$$
where $\epsilon \sim \mathcal{N}(0, I)$ at every step.
There are various methods of scheduling $\beta_t$.

---

### Extension to Conditional Diffusion

Conditional diffusion is just diffusion, but instead of passing only $t$ to the denoising model, we also pass in a context vector.

---
---
---

## Diffusion Policies - Diffusion for Behaviour Cloning [$_1$](https://arxiv.org/pdf/2303.04137v4)

Behaviour Cloning is hard because:
- Multimodal action distributions
- Distribution shift in low data regimes
- Idle actions - during the start of demonstrations, there may be timesteps where no actions are taken. 1 step BC tends to overfit to this and careful clipping of trajectories must happen.

Diffusion gives us these benefits:
- High dimension output space - we can now predict actions for many steps into the future
- Explainability - we can see what the policy is about to do
- Stable training - not sure if this is a perk because behaviour cloning already uses static distributions
- Can use direct position control - leverage the power of low level optimal control for robot positioning and RL for high level control

**How does it work?**

We try to predict a sequence of actions, $x_t = (x_0, x_1, x_2, ...).







