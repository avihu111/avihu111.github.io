# Ideas

### (Approximate) Automatic bounding

$$
\lim_{t\to\infty}\sum_t \gamma^t =  \frac{1}{1-\gamma} \\
\parallel \langle u, v \rangle \parallel^2 \le \langle u, u \rangle \cdot \langle v, v \rangle
$$

If the algol recognises an instance of LHS then it should replace it with RHS.
(does need a notion of which is 'simpler' as we are telling it which is better)

Optimising to find tighter bounds. Searching through inequalities, like cauchy schwarz, to find relationswith less 'slip'.


### ???
