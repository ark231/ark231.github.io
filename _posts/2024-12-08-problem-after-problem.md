---
layout: post
title:  "一難去ってまた一難"
date:   2024-12-08 19:39 +0900
categories: programming BDR
author: ark231
---
最近忙しくて趣味プロジェクトに避ける時間が取れなかったが、ようやく手を付けたところ、また壁にぶつかった。
<!-- more -->
中断前にDMAが動いたので、今度はCPU時間を完全に取らなくするようにすることに取り掛かった。DMAではRXかTXのどちらかしか自動化できないので、もう片方はCPUの方でどうにかしないといけなかった。  
そこで前回はTXバッファが空になったときの割り込みハンドラでTXバッファにダミーのデータを書き込んで対処していた。しかし、それでは割り込みがかかりまくって結局DMAの恩恵があまり受けられないので、別の方法を取る必要があった。  
この問題を解決するためのヒントもサンプルコードの中にあった。サンプルコードでは、TXのみのモードに変更することでこの問題を回避していた。そして、問題のSPIフラッシュの読み書きコマンドも、データのやり取りをするときはRXのみ、TXのみしかデータが流れない。  
そこで、まずはDMAなしのfor文で1バイトずつ読み取っているバージョンで試すことにした。コマンドとアドレスはとりあえず普通に送って、その後受信のみのモードに変更して動作を確認した。  
結果、書き込み直後の1回目の読み取りは完璧に動作するものの、2回目以降は正常なデータの前に1バイトだけ謎のデータが入り、全体が1バイトずれてしまうという動作が何回実行しても再現した。訳がわからない。読み取る前に1バイト無視すればいいかと思えば最初の1回だけは謎バイトが入らないので失敗してしまう。原因がまるで見当がつかない。
