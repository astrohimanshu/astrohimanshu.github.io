---
title: "Monte Carlo Sampling of a 3D Shape using Metropolis–Hastings"
date: 2026-03-15
author: Himanshu
tags: ["monte-carlo", "mcmc", "metropolis-hastings", "computational-geometry", "python"]
image: "/images/blogs/mcmc-tung/mesh_model.png"
---

🎲🐄 **Can random sampling recover the shape of a 3D object?**

In this experiment I explored a simple idea: instead of rendering a 3D mesh directly, define a probability distribution around the mesh and use Monte Carlo sampling to explore it.

If the probability distribution is designed carefully, the samples themselves reveal the shape of the object.

This project combines ideas from:

- Monte Carlo methods  
- Markov Chain Monte Carlo (MCMC)  
- computational geometry  
- distance fields  

---

# Problem Statement

Given a 3D mesh representing an object, we want to construct a probability distribution whose high-density regions correspond to the surface of that mesh.

Instead of explicitly drawing the surface, we aim to **recover the geometry through sampling**.

---

# Monte Carlo Methods

Monte Carlo methods approximate mathematical quantities using random sampling.

Consider the integral

$$
I = \int f(x)dx
$$

If we can draw samples

$$
x_1, x_2, \dots, x_N
$$

from a distribution $p(x)$, we can estimate the integral as

$$
I \approx \frac{1}{N} \sum_{i=1}^{N} \frac{f(x_i)}{p(x_i)}
$$

As the number of samples increases, the approximation converges to the true value.

Monte Carlo methods are especially useful when:

- the dimensionality is high
- analytic integration is difficult
- the domain has complex geometry.

---

# Sampling from Difficult Distributions

Often we want to sample from a probability distribution

$$
p(x)
$$

but we only know it up to a constant

$$
p(x) = \frac{1}{Z} f(x)
$$

where

$$
Z = \int f(x)dx
$$

is the normalization constant.

Computing $Z$ is often extremely difficult.

Markov Chain Monte Carlo methods allow us to sample from such distributions **without computing the normalization constant**.

---

# Markov Chain Monte Carlo

MCMC constructs a **Markov chain** whose stationary distribution is the target distribution $p(x)$.

A Markov chain is a sequence

$$
x_0, x_1, x_2, \dots
$$

where each state depends only on the previous state.

After enough iterations, the samples produced by the chain follow the desired distribution.

One of the most widely used MCMC algorithms is **Metropolis–Hastings**.

---

# Metropolis–Hastings Algorithm

Suppose we want to sample from $p(x)$.

### Initialization

Choose an initial point

$$
x_0
$$

### Proposal step

Generate a candidate

$$
x' = x + \epsilon
$$

where

$$
\epsilon \sim \mathcal{N}(0, \sigma^2 I)
$$

### Acceptance probability

Accept the proposal with probability

$$
\alpha = \min \left(1, \frac{p(x')}{p(x)} \right)
$$

If accepted

$$
x_{t+1} = x'
$$

otherwise

$$
x_{t+1} = x_t
$$

Over time the chain begins to explore regions of high probability.

---

# From Geometry to Probability

To illustrate the idea, we look at the object in two geometric representations.

<div style="display:flex; gap:20px;">

<div style="flex:1;">
<img src="/images/blogs/mcmc-tung/clay_model.png" alt="Clay Model">
<p style="text-align:center;"><em>Clay style smooth rendering</em></p>
</div>

<div style="flex:1;">
<img src="/images/blogs/mcmc-tung/mesh_model.png" alt="Triangle Mesh">
<p style="text-align:center;"><em>Triangle mesh representation</em></p>
</div>

</div>

The clay render shows the smooth surface of the object, while the mesh visualization reveals how the geometry is actually represented using triangles.

A mesh consists of:

- **Vertices** – points in 3D space  
- **Faces** – triples of vertices forming triangles  

Thousands of triangles approximate the object's surface.

---

# Defining a Distribution from Geometry

For any point in space

$$
x = (x, y, z)
$$

we compute the distance to the mesh surface

$$
d(x)
$$

Using this distance we define the probability distribution

$$
p(x) \propto e^{-\lambda |d(x)|}
$$

where $$ \lambda$$ controls how quickly probability decreases away from the surface.

This distribution ensures that

- points close to the surface have high probability
- points far away have low probability.

---

# Monte Carlo Reconstruction

Using the Metropolis–Hastings algorithm, we generate thousands of samples from the distribution defined above.

Each step proposes a small random movement in space and accepts or rejects it depending on the probability ratio.

Over time the Markov chain spends most of its time near the mesh surface.

The resulting point cloud reveals the shape of the object.

![Monte Carlo Samples](/images/blogs/mcmc-tung/sampled_distribution.png)

Even though the mesh was never rendered directly, the geometry emerges from the density of sampled points.

---

# Why This Works

The exponential term

$$
e^{-\lambda |d(x)|}
$$

creates a probability field concentrated near the surface.

Monte Carlo sampling naturally spends more time in regions of high probability.

As a result the sampled points form a shell around the object, revealing its structure.

---

# Final Thoughts

This experiment demonstrates how ideas from probability, geometry, and Monte Carlo methods can interact.

Instead of explicitly rendering a surface, we define a probability landscape around it and allow random sampling to explore that landscape.

Over time the structure of the object emerges from the distribution of samples.
