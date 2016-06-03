# Literature

This markdown is dedicated to summing up interesting points from literature:
- interesting training techniques
- new architectures
- any cool trends from ICLR...

## Adding Gradient Noise Improves Learning for Very Deep Networks

### Technique

Adding time-dependent Gaussian noise to the gradient g at every
training step t:
```
grad[t] = grad[t] + N(0, Sigma[t]**2)
```
Adding annealed Gaussian noise by decaying the variance works better
than using fixed Gaussian noise:
```
Sigma[t]**2 = mu / (1 + t)**gamma
```

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
