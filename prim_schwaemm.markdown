---
layout: default
title: Schwaemm, Authenticated Encryption with Associated Data
permalink: /schwaemm
---

`Sparkle` is a family of cryptographic permutations, each operating on a different block size (256, 384 or 512 bits). They rely only on Addition, Rotations and XORs (ARX paradigm). It is possible to write a unique implementation for all variants that simply takes the block size and the number of steps as inputs.
It relies on the 64-bit transformation we called [Alzette](/alzette) and denote it $$A_c$$. The picture below represents the round function of a `Sparkle` instance operating on $$64×n_b$$ bits.

<img src="/assets/sparkle-round.png" width="500" alt="A diagram of the round function of `Sparkle`">
