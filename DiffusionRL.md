# Diffusion Reinforcement Learning

## What is diffusion?

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

**Why does this matter?**

- Because you're not constrained by distributions that limit representation power:
  - VAE, VQVAE
- Autoregressive in manifold space, not output space.

### Some Math

Forward diffusion (noising) process is formally defined as:

$$
  q(x | x_{t-1}) = \mathcal{N} (\sqrt{1 - \beta_t} x_{t-1}, \beta_t I)
$$
ie: scale down original image, add noise.