---
layout: default
title: (XO)Esch, an (Extendable Output) Hash Function
permalink: /esch
is_primitive: true
---

`Esch` is a family of hash functions. `Esch256` outputs digests of 256 bits, and `Esch384` of 384 bits. They rely on the [sponge construction](https://en.wikipedia.org/wiki/Sponge_function), like the current hash standard [SHA-3](https://en.wikipedia.org/wiki/SHA-3).

`XOEsch` is a family of *Extendable Output Functions (XOF)* based on `Esch`. A XOF is essentially a hash function for which the output size can be arbitrarily large. 
<!-- Conversely, a XOF can be seen like a stream cipher with an arbitrarily large nonce/IV. -->
<!-- but non-keyed? -->

<img src="assets/esh-xoesch-bits.png" alt="Parameters of Esch and XOEsch" />

`Esch` and `XOEsch` share the same high-level structure and differ only in the output size and domain separation constants. This structure 
is depicted in the following figures.

<img src="/assets/esch256.png" alt="Esch256" />

<img src="/assets/esch384.png" alt="Esch384" />


## On the Names

*Esch* is the name of a city in Luxembourg that is very close to the campus of the university of Luxembourg, and lies on the *Alzette* river.

<img src="/assets/alzette-desature-esch.png" width="300" alt="Map of Luxembourg" />