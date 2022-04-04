---
layout: post
title: 코드스테이츠 블록체인 엔지니어링 부트캠프 14주차 회고 
subtitle: 부트캠프 14주차 회고록 
categories: BEB
tags: [BEB, Blockchain, Bootcamp]
---

드디어 토큰을 직접 개발해보았다!!!
------------

저번 회고에 말했다시피 지금 본격적으로 블록체인을 활용해 개발을 배우고 있다.

저번주에 지갑 개발, Solidity 기본 문법과 간단한 Smart contract에 대해 배웠다면 14주차에는 본격적으로

Fungible Token (FT)와 Non-Fungible Token (NFT)에 대한 개념을 배우고 직접 개발을 해서 local environment와

testnet에 배포를 하는 것을 배웠다.

이더리움 토큰을 만들때는 ERC-20이라는 표준을 이용해서 만들기에 코드를 From the scratch에서 짜지는 않았지만

해당 standard에 포함되어 있는 함수들을 보고 어떤식으로 작동하는지 이해한 다음 openZeppelin을 이용해 개발을 해서

배포를 해봤는데 내 지갑에 내가 만든 토큰이 찍히니 뭔가 감개무량 했다. ㅋㅋㅋ

역시 이론도 중요하지만 뭔가 직접적으로 코드를 짜서 내 눈에 실제로 뭔가가 보이니 이 부분이 훨씬 재밌긴 했다.

처음은 ERC-20 standard로 이더리움기반 토큰을 만들어 봤고 이후에 KIP-7 standard를 보고 Klaytn 기반 토큰도 만들어서

Klaytn의 testnet인 baobab에 배포해보았다.

자세한 내용은 개발기 포스트에 올리겠지만 Klaytn이 Ethereum을 포크해와서 만든 플랫폼이고 KIP-7 자체도 ERC-20 standard를 많이 따랐다. (safeTransfer()와 같은 몇몇 함수를 제외하고)

그래서 많은 부분이 서로 닮아 있어서 이더리움 기반으로 학습을 해놓으니 클레이튼 기반은 별 무리 없이 진행 할 수 있었다.

그래서 개발기 포스트에는 이더리움과 클레이튼 두가지 플랫폼 모두에 대해 작성을 해보려고 한다.

### 한주동안 배운 것 ###

FT와 NFT를 실제로 이더리움과 클레이튼 기반으로 각각 표준을 사용하여 개발 해 보았다.

testnet에 각각 배포를 해서 metamask 지갑과 kaikas 지갑으로 토큰을 받아 볼 수 있었을때 너무 재밌고 신기했다.

다음주는 Decentralised Identification (DID)를 활용해서 dAPP 개발을 하는데 미리 한번 읽어보고 예습을 해보려고 한다.

### 마치며... ###

저번주에 원래 몸 컨디션을 찾게 되어서 기분 좋게 학습을 진행할 수 있어서 너무 좋았다.

역시 다시 한번 건강이 최고다라는 것을 깨달았다. 재밌다고 공부도 너무 오버페이스로 하지말고 적당히 쉬기도 하면서

조절을 해서 항상 꾸준히 할 수 있게 해야겠다.

빨리 Project를 해서 팀 단위로 dAPP 개발을 해보고 싶다!!!