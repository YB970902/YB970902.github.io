---
layout: post
title: EternalSmash - 행동트리 에디터 만들기 2
categories: [사이드 프로젝트 - EternalSmash]
tags: [사이드 프로젝트]
---

## 개요
***
> 사이드 프로젝트에 사용할 행동트리 에디터를 만들자.

이번엔 여러 타입의 노드를 만들어보겠다.

그 전에 그동안 바뀐 내용을 소개하겠다.

## 변경된 부분
***

기존에는 스크립트를 이용해서 VisualElement를 추가하는 방식으로 작성했었다. 하지만 UIBuilder를 사용해 만든다는 취지와 맞지 않아서 Uxml을 로드하여 노드를 만드는 방식으로 변경하였다.

이렇게 변경하고 나니 스타일을 적용하면 즉시 확인해볼 수 있는점이 매우 편리했다. 스타일을 바꾸기가 쉬워진김에 기존 노드의 스타일을 아예 싹 바꿨다.  
부모노드 Port를 없앴고, 배경 색상을 통일시켰다. 노드의 별명은 변경할 수 있기 때문에 배경색상을 다르게 했다.

![Editor_1](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_1.png)

## 기능 구현
***

### RootNode
***

RootNode는 자식노드와 이어지면 되고, 부모노드가 필요없다. 따라서 자식노드와 연결할 수 있는 Port만 추가한다.

![Editor_2](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_2.png)

### ControlNode
***

ControlNode는 제공하는 기능마다 보여져야하는 형태가 조금씩 다르다.

ControlNode의 종류는 None, Sequence, Selector, If, While이 있다.

None은 아무런 데이터도 보이지 않는다.

![Editor_3](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_3.png)

#### SequenceNode, SelectorNode

SequenceNode와 SelectorNode는 여러개의 자식을 가질 수 있어야 해서 동적으로 포트를 늘리고 줄일 수 있는 기능을 만들어서 추가했다.

'+'버튼을 누르면 포트가 생성되고, 포트 아래의 '-'버튼을 누르면 그 포트는 삭제된다.

![Editor_4](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_4.png)

![Editor_5](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_5.png)

#### IfNode

IfNode는 bool타입을 반환하는 함수를 호출하고 연산결과에 따라 True or False 노드를 수행한다.

어떤 함수를 호출할것인지를 저장하는 FunctionName 드롭다운과 True, False 포트를 추가했다.

![Editor_6](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_6.png)

#### WhileNode

WhileNode는 어떤 노드를 몇번 반복할것인지 저장해야한다.

IntegerField를 추가해서 반복 횟수를 저장하고, Port를 추가해서 자식과 연결할 수 있도록 했다.

![Editor_7](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_7.png)

### ExecuteNode
***

ExecuteNode는 부모노드와 연결할 포트와 어떤 기능을 실행할지 Tag를 저장해야 한다.

Dropdown으로 Tag를 저장하고, Port를 추가하여 부모노드와 연결할 수 있도록 했다.

![Editor_8](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor2_8.png)

## 마치며
***

기존의 스크립트로 Element를 추가하는 방식에서 UIBuilder를 사용하는 방법으로 변경하니, USS로 변경한 스타일을 즉시볼 수 있었다.  
또 IDE화면과 UnityEditor를 번갈아가면서 빌드를 기다리는것보다 훨씬 빠르게 결과를 확인할 수 있다는 장점이 있었다.

제대로된 튜토리얼이 없어서 기능을 제대로 파악하지 못했기 때문에 만들다가 수정하기를 반복했었다. 여기에서 시간을 많이 쏟기는 했지만, 기능이 익숙해지고 점점 결과가 나오면서 뿌듯함을 많이 느꼈다. 앞으로 정식버전으로 나올 UIToolkit이 기대가 된다.