---
layout: default
title: CRAX, a Classic Block Cipher
permalink: /crax
is_primitive: true
---

Crax is a 64-bit block cipher that uses a 128-bit secret key. Since its key
schedule is very simple and does not have to be precomputed, it is one of the
fastest 64-bit lightweight block ciphers in software, beaten only for messages
longer than 72 bytes by the NSA cipher Speck [6]. Due to this simple key
schedule, it consumes lower RAM than Speck. While the family of tweakable
block ciphers Skinny [9] can be considered as an academic alternative to the
NSA cipher Simon [6] in terms of hardware efficiency, Crax can be seen as an
academic alternative to Speck in terms of software efficiency