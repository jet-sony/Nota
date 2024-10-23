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

# What is Diffusion?

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

### Intuition

- No mode collapse.
- No naive distributions.
- Theoretically infinite modes.

![bg right:50% height:400px](.images/12473972-a33d-44e7-83de-8dd16ff23f4f.png)

---

### Intuition

![height:300px](.images/0c23a328-072c-41b6-8f5d-e8e7eaf302a6.png)

---

### Implementation 

Forward diffusion (noising)
$$
  q(x_t | x_{t-1}) = \mathcal{N} (\sqrt{\alpha_t} x_{t-1}, (1 - \alpha_t) I)
$$
Can be generalized to
$$
  q(x_t | x_0) = \mathcal{N} (\sqrt{\bar{\alpha}_t} x_0, \sqrt{1 - \bar{\alpha}_t} I)
$$

Reverse diffusion (denoising)
$$
  p_\theta (x_{t-1} | x_t) = \mathcal{N} (\mu(x_t, t), \sigma (x_t, t))
$$

---

# Diffusion Policies (Behaviour Cloning)

Diffusion for n-step action prediction

![bg right:50% width:900px](.images/619a2239-2a0c-4938-b9ac-271ea2c90cc0.png)

---

## 

---



