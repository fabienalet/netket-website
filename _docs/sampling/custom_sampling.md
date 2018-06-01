---
title: Custom Samplers
permalink: /docs/custom_sampling/
---
NetKet provides the freedom to define user’s defined samplers, just specifying the relevant stochastic operators in the `Sampler` section of the input. For the moment, this functionality is limited to $$k$$-local move operators.

$$
\mathcal{T}= \sum_i T_i,
$$

The samplers describe below can be used for any quantum system with a local (and finite) discrete Hilbert space.

<h2 class="bg-primary">Move Operators</h2>
The local move operators $$ T_i $$ are specified giving their matrix elements, and a list of sites on which they act.

The transition probability associated to a custom sampler can be decomposed into two steps:

1. One of the move operators $$ T_i $$ is chosen with probability given by the user (or uniform probability by default).  
2. Among all the possible ($$ m $$) values that $$ s_i $$ can take, one of them is chosen with uniform probability.

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `ActingOn` | List of list of integers  |  The sites on which each $$ T_i $$ acts on | None |
| `StochasticOperators` | List of floating matrices |  For each $$ i $$, the matrix elements of $$ T_i $$ in the $$ k $$-local Hilbert space | None |
| `OperatorsStochasticWeights` | List of floats |  For each $$ i $$, the probability to pick one of these move operators | uniform probability |
|===


For example, in the case of spin $$ 1/2 $$ particles, $$ m=2 $$ and the possible local values are $$ s_i = -1,+1 $$.
In this case then `MetropolisLocal` is equivalent to flipping a random spin.

In the case of bosons, with occupation numbers $$ s_i = 0, 1, \dots n_{\mathrm{max}} $$, `MetropolisLocal` would pick a random local occupation number uniformly between $$ 0 $$ and $$ n_{\mathrm{max}} $$.  


### Example
```python
#Heisenberg model on L site with nearest-neighbour exchange moves

SpSm=[[1,0,0,0],[0,0,1,0],[0,1,0,0],[0,0,0,1]]

#Now we define the move operators of our custom sampler
#And the sites on which they act
move_operators=[]
sites=[]
L=20
for i in range(L):
    operators.append(SpSm)
    sites.append([i,(i+1)%L])

pars['Sampler']={
    'ActingOn'       : sites,
    'MoveOperators'      : move_operators,
}
```

<h2 class="bg-primary">CustomSamplerPt</h2>
This sampler performs [parallel-tempering](https://en.wikipedia.org/wiki/Parallel_tempering) moves in addition to the local moves implemented in `CustomSampler`. The number of replicas can be $$ N_{\mathrm{rep}} $$ chosen by the user.

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `Nreplicas` | Integer |  The number of effective temperatures for parallel tempering, $$ N_{\mathrm{rep}} $$ | None |
|===

### Example
```python
pars['Sampler']={
    'Name'           : 'MetropolisLocalPt',
    'Nreplicas'           : 64,
}
```

<h2 class="bg-primary">MetropolisExchange</h2>
This sampler acts locally only on two local degree of freedom $$ s_i $$ and $$ s_j $$, and proposes a new state: $$ s_1 \dots s^\prime_i \dots s^\prime_j \dots s_N $$,
where in general $$ s^\prime_i \neq s_i $$ and $$ s^\prime_j \neq s_j $$ . The sites $$ i $$ and $$ j $$ are also chosen to be within a maximum graph distance of $$ d_{\mathrm{max}} $$.

The transition probability associated to this sampler can be decomposed into two steps:

1. A pair of indices $$ i,j = 1\dots N $$, and such that $$ \mathrm{dist}(i,j) \leq d_{\mathrm{max}} $$, is chosen with uniform probability.   
2. The sites are exchanged, i.e. $$ s^\prime_i = s_j $$ and $$ s^\prime_j = s_i $$.

Notice that this sampling method generates random permutations of the quantum numbers, thus global quantities such as the sum of the local quantum numbers are conserved during the sampling.
This scheme should be used then only when sampling in a region where $$ \sum_i s_i = \mathrm{constant} $$ is needed, otherwise the sampling would be strongly not ergodic.  

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `Dmax` | Integer | The maximum graph distance allowed for exchanges | 1 |
|===

### Example
```python
pars['Sampler']={
    'Name'           : 'MetropolisExchange',
}
```

<h2 class="bg-primary">MetropolisExchangePt</h2>
This sampler performs [parallel-tempering](https://en.wikipedia.org/wiki/Parallel_tempering) moves in addition to
the local exchange moves implemented in `MetropolisExchange`. The number of replicas can be $$ N_{\mathrm{rep}} $$ chosen by the user.

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `Dmax` | Integer | The maximum graph distance allowed for exchanges | 1 |
| `Nreplicas` | Integer |  The number of effective temperatures for parallel tempering, $$ N_{\mathrm{rep}} $$ | None |
|===

### Example
```python
pars['Sampler']={
    'Name'           : 'MetropolisExchangePt',
}
```
