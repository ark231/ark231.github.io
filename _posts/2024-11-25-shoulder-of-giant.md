---
layout: post
title:  "巨人の肩"
date:   2024-11-25 14:05 +0900
categories: programming BDR
author: ark231
---
DMAがついに動いた。
<!-- more -->
何を試しても動かないので、諦めて既存の実装を探したところ、funのexamplesにまさしく`spi_dma`というサンプルがあり、dmaを用いたtxの叩き台が実装されていた。何が違うのか見てみたところ、RCC_AHBPCENRというレジスタに書き込んでDMAのクロックを有効化していた。クロックは盲点だった。リファレンスマニュアルが分厚すぎてDMAとSPIの章しか印刷していなかったので、クロックをいじろうという発想にいたらなかった。
