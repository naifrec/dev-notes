# Literature

This markdown is dedicated to summing up interesting points from literature:
- interesting training techniques
- new architectures
- any cool trends from ICLR...

## Adding Gradient Noise Improves Learning for Very Deep Networks

Paper: http://arxiv.org/abs/1511.06807

### Technique

Adding time-dependent Gaussian noise to the gradient g at every
training step t:
\[
g_t = g_t + \mathcal{N}(0, \sigma^2)
\]
Adding annealed Gaussian noise by decaying the variance works better
than using fixed Gaussian noise:
\[
\sigma^2 = \frac{\mu}{(1 + t)^\gamma}
\]
`mu` can be selected from {0.01, 0.3, 1.0} and `gamma` is fixed to .55.

### Heuristics

- Adding gradient noise to very deep and complex architectures seem to help making
the training more robust.
- Shallow architectures will likely not benefit from gradient noise
- Adding gradient noise usually helps getting out of a bad initialization. Good
initialization (ReLU init) on conventional CNNs will certainly show little difference
with or without gradient noise. Again, only on more complex architecture where initialization
have not been researched yet should gradient noise help.
- Gradient noise should be **always be performed after gradient clipping**. This is likely
because the effect of random noise is washed away when gradients become too large


## Zoneout: Regularizing RNNs by Randomly Preserving Hidden Activations

Paper: https://arxiv.org/abs/1606.01305v2

### Technique

Recurrent neural networks process data $x_1, x_2, ..., x_T$ sequentially,
constructing a corresponding sequence of representations, $h_1, h_2, ..., h_T$.
The transition operator, $\mathcal{T}$ , of a recurrent neural network converts the
present hidden state and input into a new hidden state: $h_t = \mathcal{T}_t(h_{t−1}, x_t)$

Zoneout is as a modification of the transition operator, similar to dropout. For both
methods, the masking coefficient, $d_t$, is a Bernoulli random vector sampled
independently at each time-step during training, and is replaced by its expectation at test time.

- Zoneout: $\mathcal{T}_t = d_t \odot \mathcal{T}'t + (1 − d_t) \odot 1$
- Dropout: $\mathcal{T}_t = d_t \odot \mathcal{T}'t + (1 − d_t) \odot 0$
