---
layout: post
title: EternalSmash - 행동트리 에디터 만들기 1
categories: [사이드 프로젝트 - EternalSmash]
tags: [사이드 프로젝트]
---

## 개요
***
> 사이드 프로젝트에 사용할 행동트리 에디터를 만들자.

사이드 프로젝트에 사용할 행동트리를 구현했었다. 행동트리를 Builder패턴으로 생성하게끔 구현했는데, 아무리 보기좋게 코드를 작성해도 한눈에 알아보기에는 무리가 있었다. 또 코드로 작성되어있기 때문에 프로그래밍을 할 수 없다면 수정할 수 없다는 문제도 있었다.  
이런 문제를 해결하기 위해서 행동트리를 손쉽게 편집할 수 있도록 에디터가 필요해졌다.

에디터를 만드는 방법으로는 IMGUI를 사용하는 방법이 있다고 하는데, 기능이 충분하지 않아서 사용할 수 없었다. 다른 방법을 찾아보던중 요즘 유니티에서 개발하고있는 새로운 UI시스템인 UIToolkit이 에디터제작을 지원한다는 것을 알게됐다. 튜토리얼을 찾아봤는데 딱 원하는 기능을 지원하고 있어서 UIToolkit으로 제작하는것으로 결정했다.

알아야 할 것도 많고 에디터를 구현하는것은 손이 많이가는데도 사이드 프로젝트에 도입하려는 이유는 다음과 같다.

1. 이번 사이드 프로젝트의 목적이 하나하나 직접 만들어보는것이다. 에디터를 제작할 수 있으면, 유지보수 측면에서 큰 이득을 볼 수 있기 때문에 장기적으로 보면 꼭 알아야하는 기술이다.
2. 유니티의 새로운 UI시스템인 UIToolkit을 미리 접하면, 정식버전으로 출시하고나서 UI시스템의 선택지가 넓어진다.

## 기능 구현
***

### 배경 만들기
***

필자가 원하는 행동트리 에디터는 Animator처럼 격자무늬가 있는 배경에서 우클릭으로 원하는 노드를 생성하고 연결하여 사용하는것이다.

위의 내용을 구현하기 위해선, 유니티에서 제공하는 실험적인 API인 GraphView API를 이용해야한다.  
GraphView API는 실험중인 단계이기 때문에 함수명이 변하거나 구조가 바뀔수도 있다. 하지만 사용중인 사람들이 많기 때문에 크게 바뀌진 않을것이다.

GraphView 클래스를 상속받은 다음 배경을 설정하면 격자무늬를 만들 수 있다.

![Editor_1](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_1.png)

행동트리에 사용할 노드는 세부 정보들을 수정할 수 있어야 한다. Insepctor처럼 노드의 속성을 보고 수정할 수 있는 창이 있으면 편집할때 용이할것같아 추가하겠다.

TwoPaneSplitView 클래스를 상속받은 오브젝트를 이용해 화면을 가로로 분할한다음, Inspector창으로 사용할 VisualElement를 추가하면 다음과 같은 모습이 된다.

![Editor_2](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_2.png)

### 노드 만들기
***

행동트리에 사용될 노드는 크게 세 종류로 나눌 수 있다.

첫 번째 종류는 Root노드이다.  
Root노드는 최상단에 위치하며 무조건 1개만 있어야 한다. 최초로 실행되는 노드이다.

두 번째 종류는 Control노드이다.  
Control노드는 특정상황에 맞춰 자식 노드들 컨트롤하는 노드이다. 조건문, 반복문등에서 할 수 있는 기능을 적용할 수 있다.

세 번째 종류는 Execute노드이다.  
Execute노드는 행동을 하는 노드이다. 어떤 행동을할지 정의하여 원하는 시점에 수행시킬 수 있다.

세 종류의 노드는 각각 보여져야하는 정보가 다르며, Control노드의 경우 또 다시 세분화할 수 있는데, 각각 보여져야하는 정보가 다를수도 있다.

위의 내용을 구현하기 위해선 가장 기본적인 정보만 가지고 있는 베이스 노드를 만들고, 이를 상속받아서 구현할것이다.

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
또 노드의 별명은 포커스 되었을때와 그렇지 않을때 색차이를 주어 구분했다.

![Editor_5](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_5.png)

![Editor_6](/assets/images/sideProject/EternalSmash/BehaviourTreeEditor/BTEditor1_6.png)

## 마치며
***

GraphView API는 무엇이 있고, 어떻게 사용해야하는지를 익히느라 시간이 꽤 걸렸다.  
단순히 스크립트만으로 구현이 가능했지만, UIToolkit을 이용해서 구현하면 수정이 더 쉬워지기 때문에 Uxml을 불러오고 StyleSheet을 적용하는 방법을 추가로 공부했다.

다행히 HTML, CSS, JS를 다뤄본 경험이 있어서 익숙한 문법이 많아서 UIToolkit을 익히는데에는 시간을 많이 쓰지 않았다.

다음번엔 다른 타입의 노드를 추가로 만들고, GraphView에서 우클릭을 통해 노드를 생성하는 부분까지 만들어보겠다.