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