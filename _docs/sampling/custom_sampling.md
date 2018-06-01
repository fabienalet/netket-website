---
title: Custom Samplers
permalink: /docs/custom_sampling/
---
NetKet provides the freedom to define user’s defined samplers, just specifying the relevant move operators in the `Sampler` section of the input. For the moment, this functionality is limited to move operators which are sums of $$k$$-local move operators:

$$
\mathcal{T}= \sum_i T_i,
$$
where the move operators $$ T_i $$ act on an (arbitrary) subset of sites.

<h2 class="bg-primary">Move Operators</h2>
The operators $$ T_i $$ are specified giving their matrix elements, and a list of sites on which they act. The operators $$ T_i $$ must be real, symmetric, positive definite and stochastic.  

The transition probability associated to a custom sampler can be decomposed into two steps:

1. One of the move operators $$ T_i $$ is chosen with probability given by the user (or uniform probability by default).  
2. Starting from state $$ |n\rangle $$, the probability to transition to state $$ |m\rangle $$ is given by $$ \langle n | T_i | m \rangle $$.

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `ActingOn` | List of list of integers  |  The sites on which each $$ T_i $$ acts on | None |
| `MoveOperators` | List of floating matrices |  For each $$ i $$, the matrix elements of $$ T_i $$ in the $$ k $$-local Hilbert space | None |
| `OperatorProbabilities` | List of floats |  For each $$ i $$, the probability to pick one of the move operators | uniform probability |
|===


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
The custom sampler can perform [parallel-tempering](https://en.wikipedia.org/wiki/Parallel_tempering) moves in addition to the custom moves implemented in `CustomSampler`. The number of replicas can be $$ N_{\mathrm{rep}} $$ chosen by the user.

|---
| Parameter | Possible values | Description | Default value |
|-|-|-|-
| `Nreplicas` | Integer |  The number of effective temperatures for parallel tempering, $$ N_{\mathrm{rep}} $$ | None |
|===
