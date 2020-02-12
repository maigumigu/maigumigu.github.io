---
title: "IntelliJ 개꿀기능(2) -정규식으로 검색해서 코드 수정하기"
date: 2019-02-12 00:00:00 -0400
categories: IntelliJ
---

# IntelliJ 개꿀 기능(2): 정규식으로 검색해서 수정하기

정규식으로 검색할 수 있는 기능은 sublime text, vscode등의 다른 에디터들에서도 지원한다.

특별하게 IntelliJ에서는  검색된 문자열을 그룹화및 변수화하여

코드 일괄 수정이 필요한 경우 유용한 기능을 제공한다.



다음과 같은 상황을 가정해보자.

어떤 메소드의 시그니처가 변경되었는데, 파라미터 값 하나가 추가 되었다.

그런데 해당 메소드는 프로젝트 내부 호출 위치가 100개가 넘는다.

호출부를 일일이 수정하는 것은 생각만 해도 끔찍하다. 바로 이런 상황에 유용하다.

```java
//Before
instanceName.methodName(instance,"string");
//After
instanceName.methodName(instance,"string", 1);
```



## Step1. 검색할 문자열에 대한 정규식을 만든다.

```regex
instanceName.methodName\(([A-Za-z]+), (.+)\)
```

정규식 [A-Za-z]+를 그룹화 하기위해 소괄호로 묶는다. **([A-Za-z]+)**

정규식 (.+)\)를 그룹화 하기위해 소괄호로 묶는다. **(.+)**



## Step2. 변경될 문자열에 대한 정규식을 만든다.

```
instanceName.methodName\($1, $2,1\)
```

$1은 [A-Za-z]+가 대입된 문자열, $2는.+가 대입된 문자열



## Step3. Ctrl+Shift+R을 누른 후 각각의 정규식을 넣어준다.

맥은 command + R.



## Step4. Replace All 버튼을 눌러준다.

끄읕! $기호로 유용한 기능을 만들어준 IntelliJ에게 감사를!
