---
layout: post
title: EternalSmash - 행동트리 에디터 만들기 3
categories: [사이드 프로젝트 - EternalSmash]
tags: [사이드 프로젝트]
---

## 개요
***
> 사이드 프로젝트에 사용할 행동트리 에디터를 만들자.

이번엔 노드들의 포트연결과 삭제 처리를 추가하겠다.

## 포트 연결
***

포트는 만들어놓기만 한다고 해서 연결되진 않는다. 어떤 조건에 연결할것인지 설정을 해줘야 한다.  
여기에선 나 자신이 아니고, 같은 방향의 포트가 아니면 연결할 수 있게끔 설정했다.

![Editor_1](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor3_1.png)

### 포트 삭제
***

노드가 삭제된다고 해서 포트가 자연적으로 삭제되지도 않는다. 노드가 삭제되면 그 노드와 연결되어 있는 포트는 그대로 남아있는다.
OnPortRemoved라는 함수를 상속받아서 연결되어 있는 포트의 연결을 끊어야 한다.

## 노드의 삭제 처리
***

기본적으로 노드는 삭제 단축키를 입력하면 삭제된다. 하지만, 내가 만든 노드는 UIToolkit로 만든 틀을 사용하기 때문에, 노드에서 사용되는 몇몇 함수가 잘 동작하지 않을 수 있다.

그 중에 하나가 삭제 버튼이었는데, 이건 'input', 'output'이라는 이름의 VisualElement가 없어서 생기는 문제였다. 두 이름의 VisualElement를 추가해서 해결했다.

## 에러 표시
***

Execute노드와 Condition노드의 경우 추가로 타입을 설정해줘야 한다. 만약 타입을 설정하지 않았을 경우를 표시하기 위해 빨간색으로 표시했다.

![Editor_2](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor3_2.png)

## 마치며
***

목적이 툴이다보니, 사용 편의를 고려해야겠다는 생각이 들어서 에러 표시를 추가했다.

다음번엔 좌측 Inspector창에 상태를 띄우는 방법을 적용할것이다.