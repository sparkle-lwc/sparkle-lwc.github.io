---
layout: default
title: Schwaemm, Authenticated Encryption with Associated Data
permalink: /schwaemm
is_primitive: true
---

`Schwaemm` is an *Authenticated Encryption with Associated Data* (AEAD) algorithm that uses the [Sparkle](/sparkle) permutation in a specific mode. Several versions exist, each providing a specific security level and requiring a permutation operating on a specific block size. The following table summarizes the proposed instances of the family. Here, $$n$$ denotes the size of the permutation (in bits), $$r$$ denotes the *rate*, $$c$$ denotes the *capacity*,
\\(|K|, |N|, |T|\\) denote the sizes of key, nonce and tag respectively.

<!-- x| **Name** | **Security (bits)** | **Permutations size** | 
     x|------|----------------|-------------------|
     x| `Schwaemm-128-128` | 120 | 256 |
     x| **`Schwaemm-256-128`** | 120 | 384 |
     x| `Schwaemm-192-192` | 184 | 384 |
     x| `Schwaemm-256-256` | 246 | 512 | -->

<img src="/assets/schwaemm-table.png" alt="Schwaemm instances" />

All instances use (a slight variation of) the [Beetle](https://tches.iacr.org/index.php/TCHES/article/view/881) mode of operation presented, which is based on the well-known [SpongeWrap](https://link.springer.com/chapter/10.1007/978-3-642-28496-0_19) AEAD mode.

The difference between the instances is the version of the underlying `Sparkle` permutation (and thus the rate and capacity is different) and the size of
the authentication tag. The following figure shows the structure on the example of `Schwaemm256-128`.

<img src="/assets/schwaemm-figure.png" alt="Schwaemm256-128" />