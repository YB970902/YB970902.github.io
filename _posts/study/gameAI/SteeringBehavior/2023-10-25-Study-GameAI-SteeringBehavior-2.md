---
layout: post
title: 게임 AI - 조종행동(SteeringBehavior) - 2 비켜주기
categories: [공부 - 게임 AI]
tags: [조종행동]
---

## 개요
***
> 조종행동을 이용해서 장애물을 회피하는 AI를 만들자.

지난번에 설명한 조종행동의 구조와 더불어 적을 회피하는 AI를 만들어보자.

## 아이디어
***
원하는 내용은 가만히 대기하고 있을경우, 아군이 지나가면 길을 비켜주고 적군이 지나가면 길을 막는다.  
이동하고 있을 경우, 아군이나 적군이 지나가면 길을 지나친다. 길이 좁을경우엔 아군끼리는 조금 겹칠 수 있지만, 적군과 겹칠수는 없다.

위와같은 내용을 구현하려고 한다. 우선 대기하고 있을때 에이전트가 지나가면 길을 비켜주는 기능을 만들어보자.

## 구현
***

대기하고 있으며, 주변에 다른 유닛이 지나가면 비켜주는 행동을 만들어보자.

### 비켜주기
***

비켜주기의 아이디어는 간단하게 작성해보았다.

주변에 다른 에이전트가 지나가면 그 에이전트로부터 피하는 방향으로 이동하는 힘을 주는것이다.

초록색 원이 비켜주는 행동을 하는 에이전트이고, 노란색 원은 랜덤한 방향으로 꾸준히 나아가는 에이전트이다.

<iframe width="640" height="500" src="https://www.youtube.com/embed/f8BSH4RKi-k" title="조종행동-적회피" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<center>적을 피하고는 있지만, 부들부들 떠는 모습이 보인다.</center>



피하는 힘을 최대로 줘버렸더니, 피하는 모양새가 너무 어색했다.

이번엔 적과의 거리가 가까울수록 힘을 조금 주고, 멀수록 힘을 세게 주도록 코드를 수정해보았다.

<iframe width="640" height="500" src="https://www.youtube.com/embed/BcLjGWzLMo8" title="조종행동-적회피2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<center>마치 무림고수처럼 필요한 만큼만 부드럽게 피하도록 바뀌었다.</center>

힘을 어떻게 조절하느냐에 따라서 형태가 완전히 바뀌는게 신기했다.

## 마치며
***

이번엔 가만히 대기하면서 움직이는 주변의 적들을 피하는 행동을 구현했으니, 다음번엔 이동하면서 적을 회피하는 기능을 넣을것이다.

단순하게 힘을 어떻게 줬는지에 따라 에이전트의 행동이 달라지는것이 신기했다.  
이런식으로 몇번 구현하다보면서 감각을 키우다보면 원하는 행동을 하는 AI를 쉽게 만들 수 있겠다는 자신감이 생겼다.