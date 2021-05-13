---
layout: default
title: Alzette, an ARX-box
permalink: /alzette
is_primitive: true
---

`Alzette` is a 64-bit ARX-based S-box, which can be evaluated in constant time using only 12 instructions on modern CPUs. Its parallel application can also leverage vector (SIMD) instructions. One iteration of `Alzette` has differential and linear properties comparable to those of the AES S-box, and two are at least as secure as the AES *super S-box*. As the state size is much larger than the typical 4 or 8 bits, the study of the relevant cryptographic properties of Alzette was not trivial and required extensive computer experiments.

`Alzette` is parametrized by a 32-bit constant $$c$$, used 4 times in the structure.
The figures below represent the `Alzette` structure. We remind that the addition is done modulo $$2^{32}$$.

<img src="/assets/alzette-alg-fig.png" alt="The `Alzette` transformation">

`Alzette` has the following advantages:

- it relies on 32-bit rather than 16-bit operations, meaning that (according to
[Triathlon, Sect. 5](https://link.springer.com/article/10.1007/s13389-018-0193-x)) it is suitable for a larger number of architectures;
- it makes better use of *barrel shift registers* (when available) and has more
efficient rotation constants (for platforms on which they have different costs);
- its differential and linear properties are superior to those of a scaled-up
[Sparx](https://cryptolux.org/index.php/SPARX) ARX-box;
- our analysis takes more attacks into account, and is confirmed experimentally
whenever possible.

## Primitives using Alzette

Alzette is one of the main building blocks of the Sparkle-suite: it is the main source of nonlinearity and cryptographic strength in the [Sparkle permutation](/sparkle). Furthermore, it was naturally used to construct a classic block cipher [CRAX](/crax) and a tweakable block cipher [TRAX](/trax).

## Implementation Properties

`Alzette` can be executed in only 12 cycles on a
32-bit ARM Cortex-M3 and 122 cycles on an 8-bit AVR ATmega128 processor.
Besides, the code size is low: respectively 24 and 176 bytes on those platforms.

## On the Names

*Esch* is the name of a city in Luxembourg that is very close to the campus of the university of Luxembourg, and lies on the *Alzette* river.

<img src="/assets/alzette-desature-esch.png" width="300" alt="Map of Luxembourg">