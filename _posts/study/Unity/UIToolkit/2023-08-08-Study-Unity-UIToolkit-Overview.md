---
layout: post
title: UIToolkit - 0. 개요
categories: [유니티 - UIToolkit]
tags: [UIToolkit]
---

## 개요
***
> UIToolkit 대해 알아보자.

## UIToolkit이란?
***
웹 프론트엔드는 html, css, js의 조합으로 개발한다.  
html으로 큰 레이아웃을 설정하고, css로 스타일을 정의한 후에 js로 동작을 설정한다.

Unity가 여기에서 영감을 받아 제작중인 새로운 UI시스템이 UIToolkit이다.

## UIToolkit의 특징
***
UIToolkit은 현재 많은 유니티 개발자가 사용중에있는 UI시스템인 UGUI와 몇가지 차이점이 있다.

### 편집툴 제공
***
Scene View에서 작업을 해야하는 기존 UGUI방식과 달리 UIToolkit은 별도의 UIBuilder 윈도우를 제공한다.
여기에서 작업한 UI가 실제로 어떻게 동작하는지 Preview 기능을 통해 확인할 수 있다.

### 동적 텍스트 렌더
***
TestMesh Pro의 글꼴 렌더링 기술을 적용했기 때문에 포인트 크기와 해상도에 상관없이 텍스트를 선명하게 렌더링할 수 있다.
![Overview_1 image](/assets/images/study/Unity/UIToolkit/Overview/Overview_1.webp)

### 편리한 UI 애니메이션 기능 제공
***
UI에 애니메이션을 간편하게 추가할 수 있으며, USS를 이용해 자주 사용되는 애니메이션을 재사용할 수 있다.

### 선명한 텍스처리스 UI 렌더링 기술 이용
***
직접 이미지를 그리고 옮길 필요 없이, UIToolkit에서 제공하는 기능을 이용해 UI를 만들 수 있다. 테두리 라운딩, 색상, 크기, 텍스트를 설정할 수 있다.

### 커스텀 UI 및 Unity 에디터 확장 프로그램 개발
***
UIToolkit에선 커스텀 에디터를 손쉽게 개발할 수 있도록 기능을 제공해준다. 에디터를 이용한 툴제작에 용이하다.

## 마치며
***
UIToolkit은 아직 개발중인 단계이기 때문에 몇가지 버그가 있을 수 있다.

따라서 아직 상용화중인 단계의 프로젝트에 적용하는데에는 어려움이 있겠지만, 앞으로 사용하게될 새로운 방식의 UI방식에 익숙해지기 위한다는 목적으로 접근하면 좋을것같다.