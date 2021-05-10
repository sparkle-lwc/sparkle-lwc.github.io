---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title:  "Sparkle suite"
---



The Sparkle-suite is a collection of lightweight symmetric cryptographic algorithms currently in the final round of the [NIST standardisation effort](https://csrc.nist.gov/Projects/lightweight-cryptography/). It contains both [/Authenticated Ciphers with Associated Data/ (AEAD)](https://en.wikipedia.org/wiki/Authenticated_encryption#Authenticated_encryption_with_associated_data_(AEAD)) and [hash functions](https://en.wikipedia.org/wiki/Hash_function). These rely on the same inner component: the SPARKLE family of permutations. 

Their implementation on micro-controllers is both very small (low ROM/RAM footprint) and very fast (low cycle count per processed byte).

A quick description follows. For more details, please checkout the following resources.
- <github link>
- <implementation page>
- <cryptanalysis page>

## Quick Description

The SPARKLE suite consists of multiple algorithms:
- `SPARKLE` is a family of cryptographic permutations, each operating on a different block size (256, 384 or 512 bits). They rely only on Addition, Rotations and XORs (ARX paradigm). It is possible to write a unique implementation for all variants that simply takes the block size and the number of steps as inputs.
- `Esch` is a family of hash functions. `Esch256` outputs digests of 256 bits, and `Esch384` of 384 bits. They rely on the [sponge construction](https://en.wikipedia.org/wiki/Sponge_function), like the current hash standard [SHA-3](https://en.wikipedia.org/wiki/SHA-3).
- `Schwaemm`
| Name | Security (bits) | Permutations size | 
|------|----------------|-------------------|
| Schwaemm-128-128 | 120 | 256 |
| Schwaemm-256-128 | 120 | 384 |
| Schwaemm-192-192 | 184 | 384 |
| Schwaemm-256-256 | 246 | 512 |

Our algorithms are among the top performers on micro-controllers of the NIST lightweight standardization effort, see [the dedicated page]() for more information.

The security of `Esch` and `Schwaemm` relies on the interplay between the corresponding modes of operation and specific properties of the SPARKLE permutations. Thanks to the /Long Trail Strategy/, we provide strong arguments in favour of the security of these algorithms. Our claims are in line with the current state of the literature on this topic, as expalined [here]().


## The SPARKLE Team

- [Christof Beierle](https://www.crypto.ruhr-uni-bochum.de/staff/beierle.html.en), Ruhr-Universität Bochum, Germany
- [Alex Biryukov](https://www.cryptolux.org/index.php/Alex_Biryukov), DSC and SnT, University of Luxembourg, Luxembourg
- [Luan Cardoso Dos Santos](https://wwwen.uni.lu/recherche/fstm/dcs/members/luan_cardoso_dos_santos), DSC and SnT, University of Luxembourg, Luxembourg
- [Johann Johann Großschädl](https://sites.google.com/site/groszschaedl/), DSC and SnT, University of Luxembourg, Luxembourg
- [Amir Moradi](https://www.emsec.ruhr-uni-bochum.de/chair/_staff/amir-moradi/), Ruhr-Universität Bochum, Germany
- [Léo Perrin](https://who.paris.inria.fr/Leo.Perrin/)
- [Aein Rezaei Shahmirzadi](https://www.emsec.ruhr-uni-bochum.de/chair/_staff/Aein-RezaeiShahmirzadi/), Ruhr-Universität Bochum, Germany
- [Aleksei Udovenko](https://affine.group/), CryptoExperts, France
- [Vesselin Velichkov](https://www.research.ed.ac.uk/en/persons/vesselin-velichkov)
- [Qingju Wang](https://wwwen.uni.lu/snt/people/qingju_wang), DSC and SnT, University of Luxembourg, Luxembourg


## On the Names

"Esch" is the name of a city in Luxembourg that is very close to the campus of the university of Luxembourg. "Schwaemm" is Luxembourgish for "sponges". As for "sparkle", it illustrates the fact that this family permutation is a successor of the block cipher `SPARX`: it is *SPAR*x, but *K*ey*L*ess.


## Sample

To show how lightweight our suite is, here is a full implementation of all the permutations of the `SPARKLE` family.

```c
#define MAX_BRANCHES 8
#define ROT(x, n) (((x) >> (n)) | ((x) << (32-(n))))
#define ELL(x) (ROT(((x) ^ ((x) << 16)), 16))

// Round constants
static const uint32_t RCON[MAX_BRANCHES] = {      \
  0xB7E15162, 0xBF715880, 0x38B4DA56, 0x324E7738, \
  0xBB1185EB, 0x4F7C7B57, 0xCFBFA1C8, 0xC2B3293D  \
};

void sparkle(uint32_t *state, int nb, int ns)
{
  int i, j;  // Step and branch counter
  uint32_t rc, tmpx, tmpy, x0, y0;
  
  for(i = 0; i < ns; i ++) {
    // Counter addition
    state[1] ^= RCON[i%MAX_BRANCHES];
    state[3] ^= i;
    // ARXBox layer
    for(j = 0; j < 2*nb; j += 2) {
      rc = RCON[j>>1];
      state[j] += ROT(state[j+1], 31);
      state[j+1] ^= ROT(state[j], 24);
      state[j] ^= rc;
      state[j] += ROT(state[j+1], 17);
      state[j+1] ^= ROT(state[j], 17);
      state[j] ^= rc;
      state[j] += state[j+1];
      state[j+1] ^= ROT(state[j], 31);
      state[j] ^= rc;
      state[j] += ROT(state[j+1], 24);
      state[j+1] ^= ROT(state[j], 16);
      state[j] ^= rc;
    }
    // Linear layer
    tmpx = x0 = state[0];
    tmpy = y0 = state[1];
    for(j = 2; j < nb; j += 2) {
      tmpx ^= state[j];
      tmpy ^= state[j+1];
    }
    tmpx = ELL(tmpx);
    tmpy = ELL(tmpy);
    for (j = 2; j < nb; j += 2) {
      state[j-2] = state[j+nb] ^ state[j] ^ tmpy;
      state[j+nb] = state[j];
      state[j-1] = state[j+nb+1] ^ state[j+1] ^ tmpx;
      state[j+nb+1] = state[j+1];
    }
    state[nb-2] = state[nb] ^ x0 ^ tmpy;
    state[nb] = x0;
    state[nb-1] = state[nb+1] ^ y0 ^ tmpx;
    state[nb+1] = y0;
  }
}
```
