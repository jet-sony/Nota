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

![bg right:50% height:300px](.images/40cce28f-12e8-437c-bcd2-f3b5cb15ef06.png)

- Forward diffusion (noising)
$$
  q(x_T) = \Pi_{t=1}^T q(x_t | x_{t-1})
$$
- Reverse diffusion (denoising)
$$
  p_\theta(x_0) = \Pi_{t=1}^T p_\theta(x_{t-1} | x_t)
$$

---

## Intuition

Can choose from $n$ different modalities at every denoising step.

![bg right:50% height:400px](.images/12473972-a33d-44e7-83de-8dd16ff23f4f.png)

---

![bg height:300px](.images/0c23a328-072c-41b6-8f5d-e8e7eaf302a6.png)

---

