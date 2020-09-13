---
layout: post
title: Automatic integration
---


### (approximate) Automatic integration

Most integrals are expensive to evaluate. Integrals like ??? are an exception.
Monte carlo approximation, ... are commmonly used tools for approximating

Variational inference.
Rather than maximising the hard to evaluate integral, maximise its lower bound. This ensures the ...

But how can we find these lower (or upper) bounds? We want to trade-off their accuracy for their computational cost.

- Is it possible to make a recursive version of integration by parts? $u(x), v(x): \int u dv = uv - \int vdu$.

Integration problems.
- Have distribution Q, want estimate of x under distribution P. Where there exists some relationship between $P$ and $Q$. e.g. $P = f(Q)$.
- Space is to large to sum over. Use some combination of adaptive step sizes + dim reduction?!
-
