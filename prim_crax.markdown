---
layout: default
title: CRAX, a Classic Block Cipher
permalink: /crax
is_primitive: true
---

`Crax-S-10` is a 64-bit block cipher that uses a 128-bit secret key intended for the most constrained micro-controllers. Since its key schedule is very simple and does not have to be precomputed, it is one of the fastest 64-bit lightweight block ciphers in software, beaten only for messages longer than 72 bytes by the NSA cipher [Speck](https://doi.org/10.1145/2744769.2747946). Due to this simple key schedule, it consumes lower RAM than `Speck`. While the family of tweakable block ciphers [Skinny](https://link.springer.com/chapter/10.1007/978-3-662-53008-5_5) can be considered as an academic alternative to the NSA cipher [Simon](https://doi.org/10.1145/2744769.2747946) in terms of hardware efficiency, `Crax-S-10` can be seen as an academic alternative to `Speck` in terms of software efficiency.


## Description

`Crax-S-10` is built as a key-alternating cipher on top of the [Alzette](/alzette) ARX-box. It has 10 steps (calls to `Alzette` with 5 different constants) alternated with key and constant XORs.

The following `C` code is a reference implementation for `Crax-S-10`.

```c
#define ROT(x, n) (((x) >> (n)) | ((x) << (32-(n))))

#define ALZETTE(x, y, c)                    \
  (x) += ROT((y), 31), (y) ^= ROT((x), 24), \
  (x) ^= (c),                               \
  (x) += ROT((y), 17), (y) ^= ROT((x), 17), \
  (x) ^= (c),                               \
  (x) += (y),          (y) ^= ROT((x), 31), \
  (x) ^= (c),                               \
  (x) += ROT((y), 24), (y) ^= ROT((x), 16), \
  (x) ^= (c)

#define ALZETTE_INV(x, y, c)                \
  (x) ^= (c),                               \
  (y) ^= ROT((x), 16), (x) -= ROT((y), 24), \
  (x) ^= (c),                               \
  (y) ^= ROT((x), 31), (x) -= (y),          \
  (x) ^= (c),                               \
  (y) ^= ROT((x), 17), (x) -= ROT((y), 17), \
  (x) ^= (c),                               \
  (y) ^= ROT((x), 24), (x) -= ROT((y), 31)

#define N_STEPS 10

static const uint32_t RCON[5] = {                             \
  0xB7E15162, 0xBF715880, 0x38B4DA56, 0x324E7738, 0xBB1185EB };

void craxs10_enc_ref(uint32_t *xword, uint32_t *yword, const uint32_t *key)
{
  int step;
  
  for (step = 0; step < NSTEPS; step++) {
    xword[0] ^= step;
    if ((step % 2) == 0) {
      xword[0] ^= key[0];
      yword[0] ^= key[1]; 
    } else {
      xword[0] ^= key[2];
      yword[0] ^= key[3];
    }
    ALZETTE(xword[0], yword[0], RCON[step%5]);
  }
  xword[0] ^= key[0];
  yword[0] ^= key[1];
}

void craxs10_dec_ref(uint32_t *xword, uint32_t *yword, const uint32_t *key)
{
  int step;
  
  xword[0] ^= key[0];
  yword[0] ^= key[1];
  for (step = NSTEPS-1; step >= 0; step--) {
    ALZETTE_INV(xword[0], yword[0], RCON[step%5]);
    if ((step % 2) == 0) {
      xword[0] ^= key[0];
      yword[0] ^= key[1]; 
    } else {
      xword[0] ^= key[2];
      yword[0] ^= key[3];
    }
    xword[0] ^= step;
  }
}
```

## Performance

`Crax-S-10` is a very lightweight block cipher, arguably one of the the lightest
ever reported in the literature when it comes to micro-controller implementations. The code size, RAM consumption, and execution time of `Crax-S-10` on
an 8-bit AVR and a 32-bit ARM Cortex-M3 micro-controller are summarized in
Table 11, along with those of five other lightweight block ciphers, namely [Speck64/128](https://doi.org/10.1145/2744769.2747946), [Simon-64/128](https://doi.org/10.1145/2744769.2747946), [Rectangle](https://link.springer.com/article/10.1007/s11432-015-5459-7), [Sparx](https://link.springer.com/chapter/10.1007/978-3-662-53887-6_18), and [Hight](https://link.springer.com/chapter/10.1007/11894063_4).

<img src="/assets/crax-benchmark.png" alt="Crax benchmark" />

