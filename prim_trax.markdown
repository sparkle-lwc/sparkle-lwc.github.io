---
layout: default
title: TRAX, a Tweakable Block Cipher
permalink: /trax
is_primitive: true
---

`Trax` is a tweakable block cipher operating on a larger state of 256-bit blocks.
It applies a 256-bit key and 128-bit tweak. To the best of our knowledge, the only
other large tweakable block cipher is Threefish which was used as a building for
the SHA-3 candidate Skein [33]. Unlike this cipher, `Trax` uses 32-bit words that
are better suited for vectorized implementation as well as on micro-controllers.
Another improvement of `Trax` over Threefish is the fact that we provide strong
bounds for the probability of all linear trails and all (related-tweak) differential trails.
Because of its Substitution-Permutation Network structure, `Trax` is
indeed inherently easier to analyze. Such a large tweakable block cipher can provide robust authenticated encryption, meaning that it can retain a high security
level even in case of nonce misuse or in the presence of quantum adversaries, as
argued in [15]. The performance penalty of such guarantees can be minimized
using vectorization and/or parallelism.

