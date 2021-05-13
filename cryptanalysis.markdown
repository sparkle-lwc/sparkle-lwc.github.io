---
layout: default
title: Security Analysis
permalink: /security
---

## Security Foundations

Security of the Sparkle Suite of lightweight cryptographic primitives is based on several foundations.


### The Security of Sponges

The security of our schemes is based on the security of the underlying cryptographic permutations and the security of sponge-based modes, more precisely the sponge-based hashing mode and the `Beetle` mode for authenticated encryption. The sponge-based approach has received a lot of attention as it the one used by the latest NIST-standardized hash function, `SHA-3`. We re-use this approach to leverage both its low memory footprint and the confidence cryptographers have gained for such components. The `Beetle` mode was developed more recently. It allows to process a large amount of data per call to the underlying permutation and thus perfectly fits for the lightweight cryptography purposes.
Both modes provably reduce the security of the primitives to the security of the underlying cryptographic permutation.


### The Literature on Block Cipher Design

The design of the `Sparkle` family of permutations is based on the decades old SPN structure which allows us to decompose its analysis into two stages: first the study of its substitution layer, and, second, the study of its linear layer. The latter combines the Feistel structure, which has been used since the publication of the DES, and a linear permutation with a high branching number, like a vast number of SPNs such as the AES. To combine these two types of subcomponents, we rely on the design strategy that was used for the block cipher [Sparx](https://cryptolux.org/index.php/SPARX): *the long trail strategy*. Our substitution layer operates on 64-bit branches using the ARX-based S-box [Alzette](https://link.springer.com/chapter/10.1007%2F978-3-030-56877-1_15), where ARX stands for (modular) Addition, Rotation and XOR. The study of the differential and linear properties of modular addition in the context of block cipher can be traced back to the late 90's. The fact that the block size of the ARX component is limited to 64 bits means that it is possible to investigate it thoroughly using computer assisted methods. The simplicity and particular shape of the linear layer then allows us to deduce the properties of the full permutation from those of the 64-bit `Alzette` ARX-box.


### Components Tailored for their Use Cases

When using a permutation in a mode of operation, two approaches are possible. We can use a *hermetic* approach, meaning that no distinguishers are known to exist against the permutation. This security then carries over directly to the whole function (e.g. to the whole hash function or AEAD scheme). The downside in this case is that this hermetic strategy requires an expensive permutation which, in the context of lightweight cryptography, may be too much.

At the opposite, we can use a permutation which, on its own, cannot provide the properties needed. The security is then provided by the coupling of the permutation and the mode of operation in which it is used. For example, the recently announced winner of the CAESAR competition [ASCON](https://ascon.iaik.tugraz.at/) and the third-round CAESAR candidate [Ketje](https://keccak.team/ketje.html), both authenticated ciphers, use such an approach. The advantage in this case is a much higher efficiency as we need fewer rounds of the permutation. However, the security guarantees are a priori weaker in this case as it is harder to estimate the strength needed by the permutation.

For `Sparkle` (and thus for both `Esch` and `Schwaemm`) we use the latter approach: the permutation used has a number of rounds that may allow the existence of some distinguishers. However, using a novel application of the established `long trail strategy`, we are able to prove that our sponge-based algorithms are safe with regard to the most important attack vectors - `differential attacks` and `linear attacks` - with a comfortable security margin. We thus get the best of both worlds: we do not have the performance penalty of the `hermetic` approach but still obtain security guarantees similar to those of a hermetic design.


## Detailed Analysis

<!-- TBD: security of Alzette, diff/linear bounds? -->

### Security of the Sparkle Permutation

We thorougly evaluated the security of the main component of the Sparkle suite - the `Sparkle` permutation - against state-of-the-art cryptanalytic attacks.

The following table lists upper bounds on the number of steps for which we found an attack, or for which the differential and linear bounds are too low to guarentee security, breaking a security level of `b/2` bits, where `b` denotes the block size, with regard to several attack vectors. The numbers for differential and linear attacks correspond to the bounds derived by the adapted `long trail` technique.


<!-- TBD: replace refs with links? -->

| **Attack**                     | **Ref.**                                         | &nbsp;`Sparkle256`&nbsp; | &nbsp;`Sparkle384`&nbsp; | &nbsp;`Sparkle512`&nbsp; |
| :------------------------------|:-------------------------------------------------|----------------|--------------|--------------|
| [Differential cryptanalysis](https://link.springer.com/chapter/10.1007/3-540-38424-3_1)     | C:BihSha90                                       | 4              | 5            | 6            |
| Linear cryptanalysis           | EC:Matsui93                                      | 5              | 6            | 6            |
| Boomerang attacks              | FSE:Wagner99                                     | 3              | 4            | 5            |
| Truncated differentials        | FSE:Knudsen94                                    | 2              | 2            | 3            |
| Yoyo games                     | SAC:BBDRS98                                      | 4              | 4            | 4            |
| Impossible differentials       | Knudsen98deal, EC:BihBirSha99                    | 4              | 4            | 4            |
| Zero-correlation               | bogdanov2014linear                               | 4              | 4            | 4            |
| Integral and Division property | FSE:DaeKnuRij97, FSE:KnuWag02, EC:Todo15         | 4              | 4            | 4            |
|--------------------------------|--------------------------------------------------|----------------|--------------|--------------|
|                                | **\# steps slim**                                | **7**          | **7**        | **8**        |
|                                | **\# steps big**                                 | **10**         | **11**       | **12**       |

<!-- TBD: sponge-aware attacks (G&D?) -->

