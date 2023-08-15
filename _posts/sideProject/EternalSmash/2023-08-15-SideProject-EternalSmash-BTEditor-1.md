---
layout: post
title: EternalSmash - 행동트리 에디터 만들기
categories: [사이드 프로젝트 - EternalSmash]
tags: [사이드 프로젝트]
---

## 개요
***
> 사이드 프로젝트에 사용할 행동트리 에디터를 만들자.

사이드 프로젝트로 RTS장르의 게임의 만들고 있다.  
여기에 행동트리를 구현해서 사용하려고 하는데, 행동트리를 손쉽게 편집할 수 있도록 에디터를 추가하려고 한다.

IMGUI로 에디터를 만들기엔 필요한 기능을 구현할 수 없고, VisualElement와 GraphView를 이용해서 구현해야한다.  
여기에서 요즘 유니티에서 개발중인 새로운 UI시스템인 UIToolkit을 이용하여 구현할 것이다.

에디터를 구현하는것은 손이 많이가고, 알아야 할 것도 많은데 사이드 프로젝트에 도입하려는 이유는 다음과 같다.

1. 사이드 프로젝트의 목적이 역량상승이기 때문에 평소에 접해보지 못한것을 사용해본다.
2. 유니티의 새로운 UI시스템인 UIToolkit을 미리 접하여서, 정식버전으로 출시했을때 적응을 쉽게 한다.
3. 에디터를 제작할 수 있으면, 유지보수 측면에서 큰 이득을 볼 수 있기 때문에 배운다.

앞으로 작성할 행동트리 에디터 관련글은 UIToolkit의 사용법을 정리하는것이 아닌, 어떻게 구현하고 있는지 과정을 설명하는 글이다.  
때문에 사용법을 익히고 싶다면 다른 튜토리얼을 보는것을 권장한다.

## 기능 구현
***

### 배경 만들기
***

제작하려는 행동트리 에디터는 Animator처럼 격자무늬가 있는 배경에서 우클릭으로 원하는 노드를 생성하고 연결하여 사용하는것이다.

위의 내용을 구현하기 위한 가장 기본적인 내용인 격자무늬의 배경을 만들어야한다. 유니티에서 실험적인 API인 GraphView API를 제공하고 있는데, 여기에서 GraphView 클래스를 상속받아 사용하면 구현할 수 있다.

실험중인 단계이기 때문에 함수명이 변하거나 구조가 바뀔수도 있다. 하지만 사용중인 사람들이 많기 때문에 크게 바뀌진 않을것같다.

배경을 만들고, 격자무늬 색상을 지정하면 다음과 같은 모습이 된다.

![Editor_1](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_1.png)

노드를 생성하고 나서 이름을 수정하거나, 노드의 타입을 수정하기 위해선 현재 노드가 어떤 상황인지 볼 수 있어야 하고, 값을 수정하기도 편해야 한다.  
Inspector 창에서 게임오브젝트의 속성을 볼 수 있는것처럼 보여지면 수정하기 수월할것이라 생각되기 때문에, Inspector창의 역할을 할 UI를 추가하겠다.

TwoPaneSplitView 클래스를 상속받은 오브젝트를 이용해 화면을 가로로 분할한다음, Inspector창으로 사용할 VisualElement를 추가하면 다음과 같은 모습이 된다.

![Editor_2](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_2.png)

### 노드 만들기
***

행동트리에 사용될 노드는 크게 세 종류로 나눌 수 있다.

첫 번째 종류는 Root노드이다.  
Root노드는 최상단에 위치하며 무조건 1개만 있어야 한다. 최초로 실행되는 노드이다.

두 번째 종류는 Conditional노드이다.  
Conditional노드는 특정상황에 맞춰 자식 노드들 컨트롤하는 노드이다. 조건문, 반복문등에서 할 수 있는 기능을 적용할 수 있다.

세 번째 종류는 Execute노드이다.  
Execute노드는 행동을 하는 노드이다. 어떤 행동을할지 정의하여 원하는 시점에 수행시킬 수 있다.

세 종류의 노드는 각각 보여져야하는 정보가 다르다. 심지어 세 종류를 또다시 세분화할 수 있는데, 그마저도 보여져야하는 정보가 다를 수 있다.  
따라서 최소 세 종류의 노드를 만들어야 한다.

우선 GraphViewAPI에서 제공하는 Node클래스를 상속받아 화면에 보여지게 하면 다음과 같이 보인다.

![Editor_3](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_3.png)

모든 노드가 기본적으로 가지고 있으며 보여줘야하는 데이터로는 노드의 이름, 노드의 별명, 부모노드가 있다.

* 노드의 이름
    * 노드의 이름은 이 노드가 어떤 종류의 노드인지 구분하기위해 필요하다.
* 노드의 별명
    * 노드의 별명은 이 노드가 어떤 역할을 위해 있는지 구분하기 위해 필요하다.
* 부모 노드
    * 부모 노드가 어떤 노드인지 알아야 판정 결과를 반환할 수 있으므로 필요하다.

BTEditorNode라는 이름의 가장 기본적으로 필요한 데이터들을 가지고있는 노드를 만들어보자.

![Editor_4](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_4.png)

부모 노드를 설정하기 위해 ParentNode 포트를 추가했다.

"EditorNode"라고 적힌 필드는 노드의 별명을 입력받을 수 있는 필드이다.

BTEditorNode는 해당 노드의 이름이다. 노드의 종류에 맞게 설정되며, 노드의 별명과 달리 임의로 수정할 수 없다.

Port와 노드의 별명부분은 배경이 투명해서 비쳐보이므로 StyleSheet를 이용하여 배경에 색을 넣었다.  
또 노드의 별명은 포커스 되었을때와 그렇지 않을때 색차이를 주었다.

![Editor_5](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_5.png)

![Editor_6](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_6.png)

## 마치며
***

GraphView를 만들고, 거기에 노드를 추가하는것을 이해하느라 시간이 꽤 걸렸다.  
또, UIToolkit을 이용해 Uxml을 불러오고 StyleSheet을 적용하는 방법을 이해하는데에도 시간이 추가로 걸렸다.

다행히 HTML, CSS, JS를 다뤄본 경험이 있어서 몇몇은 익숙한 문법이라 쉽게 넘어간 점도 있었다.

다음번엔 다른 타입의 노드를 추가로 만들고, GraphView에서 우클릭을 통해 노드를 생성하는 단계까지 만들어보겠다.