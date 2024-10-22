---
marp: true
paginate: true
header: Sony AI - Confidential

---

# Diffusion RL

---

## Agenda

1. What is Diffusion?
2. Diffusion Policy (Behaviour Cloning)
3. Classifier Guided Diffusion
4. Diffusion Offline RL
5. Diffusion Online RL

---

## What is Diffusion?

A technique for modelling complex multidimensional distributions.

<div style="text-align: center;">
  <img src="https://lilianweng.github.io/posts/2021-07-11-diffusion-models/DDPM.png" alt="drawing" height="200"/>
</div>

- Reverse diffusion (noising)
$$
  q(x_T) = \Pi_{t=1}^T q(x_t | x_{t-1})
$$
- Forward diffusion (denoising)
$$
  p_\theta(x_0) = \Pi_{t=1}^T p_\theta(x_{t-1} | x_t)
$$

---

---