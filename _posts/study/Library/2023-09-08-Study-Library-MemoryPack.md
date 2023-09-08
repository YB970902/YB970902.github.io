---
layout: post
title: MemoryPack 정리
categories: [스터디 - MemoryPack]
tags: [MemoryPack]
---

## 개요
---
> CyGames에서 제작한 Serializer인 MemoryPack에 대해서 알아보자.

## MemoryPack 이란?
---
MemoryPack은 C#과 유니티에서 사용할 수 있는 Binary Serializer이다.
일반적인 타입을 직렬화 했을때 다른 Serializer보다 2~5배 빠르며, 최대 10배 까지도 성능차이가 난다. 구조체 배열의 경우 50에서 최대 200배 빠르다.
MemoryPack이 이처럼 빠른 이유는, MemoryPack을 만든 CyGames가 그동안 3개의 Serializer를 만든 경험이 있기 때문이다. 이번 MemoryPack에는 제로 인코딩 방식을 채택하여 속도를 높였다고 한다.
![MemPack_1 image](/assets/images/study/Library/MemoryPack/MemoryPack_1.png)

## 설치방법
---
> 유니티에 MemoryPack을 설치해보자

유니티 에디터 상단바의 Window/Package Manager를 클릭한다.
![MemPack_2 image](/assets/images/study/Library/MemoryPack/MemoryPack_2.png)

좌측 상단 ‘+’ 버튼을 누르고 ‘Add package from git URL’을 누른다.
![MemPack_3 image](/assets/images/study/Library/MemoryPack/MemoryPack_3.png)

그뒤 아래와 같은 주소를 입력하고 ‘Add’버튼을 누른다.
[https://github.com/Cysharp/MemoryPack.git?path=src/MemoryPack.Unity/Assets/Plugins/MemoryPack](https://github.com/Cysharp/MemoryPack.git?path=src/MemoryPack.Unity/Assets/Plugins/MemoryPack)

![MemPack_4 image](/assets/images/study/Library/MemoryPack/MemoryPack_4.png)

Unsafe.dll이 없으면 오류 메시지가 뜨는데, 아래 링크를 타고 들어가서 다운받은 다음 유니티 프로젝트의Assets/Plugin 경로에 집어넣으면 된다. (이때 **.meta** 파일이 아닌지 잘 확인하고 다운받는다)
[https://github.com/Cysharp/MemoryPack/tree/main/src/MemoryPack.Unity/Assets/Plugins](https://github.com/Cysharp/MemoryPack/tree/main/src/MemoryPack.Unity/Assets/Plugins)
![MemPack_5 image](/assets/images/study/Library/MemoryPack/MemoryPack_5.png)

에러 메시지가 사라지면 설치는 끝났다.

## 사용방법
---
> MemoryPack 문서에서 제공하는 사용방법중 몇가지만 알아보자.

### 직렬화할 대상 만들기
---
직렬화할 구조체나 클래스를 **partial** 키워드를 이용하여 정의하고 **MemoryPackable** 어트리뷰트를 추가한다

```csharp
using MemoryPack;

[MemoryPackable]
public partial class Person
{
		public int Age { get; set; }
		public string Name { get; set; }
}
```
직렬화할 구조체나 클래스를 만들었으면, 이를 인스턴스화하고 직렬화/역직렬화를 해본다.
```csharp
var v = new Person() { Age = 40, Name = "John" };
var bin = MemoryPackSerializer.Serialize(v);
var val = MemoryPackSerializer.Deserialize<Person>(bin);
```
### 필드의 직렬화
---
일반적으로 [MemoryPackable] 어트리뷰트가 구조체나 클래스에 붙어있으면, public 프로퍼티나, public 필드를 자동적으로 직렬화한다.
[MemoryPackIgnore] 어트리뷰트를 직렬화 대상에 추가하여 직렬화 대상에서 제외시킬 수 있고, [MemoryPackInclude]키워드를 이용해 private 멤버를 직렬화 대상에 추가할 수 있다.
```csharp
[MemoryPackable]
public partial class Data
{
    [MemoryPackIgnore]
    public int A;
    public int B { get; set; }
    
    [MemoryPackInclude]
    private int c;
    private int d { get; set; }
}
```
실제로 직렬화 대상인 오브젝트는 <remark /> 로 보여주기 때문에 IDE를 통해 쉽게 확인할 수 있다.
![MemPack_6 image](/assets/images/study/Library/MemoryPack/MemoryPack_6.png)
Rider에서 보여주는 메시지. public 변수인 A는 Ignore되어 대상에서 제거되었고, private 변수인 c는 Include되어 대상에서 추가되었다.

### 직렬화 순서
---
MemoryPack은 멤버 이름이나 다른 정보로 직렬화 순서를 결정하는게 아닌, 필드를 정의한 순서대로 직렬화한다. 만약 순서를 변경하고 싶다면, [MemoryPackable(SerializeLayout.Explicit)]와 [MemoryPackOrder()]을 이용해 변경할 수 있다.
```csharp
// serialize Prop0 -> Prop1
[MemoryPackable(SerializeLayout.Explicit)]
public partial class SampleExplicitOrder
{
    [MemoryPackOrder(1)]
    public int Prop1 { get; set; }
    [MemoryPackOrder(0)]
    public int Prop0 { get; set; }
}
```
### 생성자 선택
---
MemoryPack은 파라미터가 있는 생성자와 없는 생성자를 모두 지원한다. 
생성자 선택 규칙은 다음과 같다.
- [MemoryPackConstructor] 어트리뷰트가 정의되어 있는 생성자를 사용한다.
- 명시적인 생성자(private 생성자 포함)가 없으면 기본 생성자를 사용한다.
- 매개변수가 있는 생성자나 기본 생성자 중에 하나만 있다면(private 생성자 포함) 그 생성자를 사용한다.
- 생성자가 여러개인경우, [MemoryPackConstructor] 어트리뷰트가 적용되어야 하며, 그렇지 않으면 오류를 발생한다.
- 매개변수가 있는 생성자를 사용해야 하는 경우, 모든 매개변수 이름은 해당 멤버 이름과 일치해야 한다.(대소문자는 구분 안한다)

```csharp
[MemoryPackable]
public partial class Person
{
    public readonly int Age;
    public readonly string Name;

    // You can use a parameterized constructor - parameter names must match corresponding members name (case-insensitive)
    public Person(int age, string name)
    {
        this.Age = age;
        this.Name = name;
    }
}

// also supports record primary constructor
[MemoryPackable]
public partial record Person2(int Age, string Name);

public partial class Person3
{
    public int Age { get; set; }
    public string Name { get; set; }

    public Person3()
    {
    }

    // If there are multiple constructors, then [MemoryPackConstructor] should be used
    [MemoryPackConstructor]
    public Person3(int age, string name)
    {
        this.Age = age;
        this.Name = name;
    }
}
```

### 직렬화 콜백
---
직렬화와 역직렬화가 시작되기 전과 후에 [MemorypackOnSerializing], [MemoryPackOnSerialized], [MemoryPackOnDeserializing], [MemoryPackOnDeserialized] 어트리뷰트를 이용해 이벤트를 받을 수 있다. static함수와 public, private 함수 모두 받을 수 있다.
단, MemoryPackOnDeserialized는 역직렬화할때 두 번째 매개변수로 오브젝트를 넘겨받아야 호출된다.

```csharp
[MemoryPackable]
public partial class Data
{
    [MemoryPackIgnore]
    public int A;
    public int B { get; set; }
    
    [MemoryPackInclude]
    private int c;
    private int d { get; set; }

    [MemoryPackOnSerializing]
    private void OnSerializeStart()
    {
        Debug.Log("MemoryPackOnSerializing");
    }
    
    [MemoryPackOnSerialized]
    private void OnSerializeEnd()
    {
        Debug.Log("MemoryPackOnSerialized");
    }
    
    [MemoryPackOnDeserializing]
    private void OnDeserializeStart()
    {
        Debug.Log("MemoryPackOnDeserializing");
    }
    
    [MemoryPackOnDeserialized]
    private void OnDeserializeEnd()
    {
        Debug.Log("MemoryPackOnDeserialized");
    }
}
```
## 마치며
---
그 외의 기능들은 MemoryPack 공식문서를 보면 알 수 있다.
[https://github.com/Cysharp/MemoryPack/tree/main](https://github.com/Cysharp/MemoryPack/tree/main)
처음으로 한글로 잘 정리된 글을 보며 배우는것이 아닌 영문으로 작성된 문서를 해석하며 글을 정리해보았다. 이해가 잘 안되고 심지어는 번역기를 돌려도 문장이 애매해져서 옮기는 과정에서 시간을 오래 썼다.
'프로그래머가 가장 먼저 배워야할 언어는 영어다'라는 우스갯소리가 왜 나오는지 제대로 알게 되었다.