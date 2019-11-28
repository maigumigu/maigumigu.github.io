---
title: "Java Collection - Comparable과 Comparator"
date: 2019-11-05 15:17:00 -0400
categories: java
---

## 문제 상황

계층 간에 데이터를 넘기고 받기 위해 DTO 객체를 자주 사용하는데,이로 인해 발생하는 상황이 있다. 

DTO 객체의 멤버 변수를 기준으로 sort를 하는 경우이다. 이런 경우는 보통 다음과 같이 처리해줬다.

```java
List<MyDTO> sortedList = TotalList
    .stream()
    .sorted(Comparator.comparing(MyDTO::getCount).reversed())
    .collect(Collectors.toList());
```

위의 코드를 설명하면 다음과 같다.

1. **stream**: 정렬이 되지 않은 리스트를 시퀀셜한 스트림형태로 변환 o1.getSelectedMyDTO().getCount()
                           .compareTo(o2.getSelectedMyDTO().getCount());

2. **sorted**: sorted 함수 안에 값을 비교하는 로직을 넣어서 정렬해줌
   - Comparator.comparing(MyDTO::getCount)
     - Comparator 클래스의 comparing 함수에 정렬의 기준이 될 멤버변수를 getter로 얻어서 넣어준다.
   -  reversed()
     - 사용한 comparator의 결과를 반대로 정렬하겠다는 뜻이다. 여기서는 오름차순으로 정렬된 결과를 내림차순으로 바꿔주겠다는 의미가 된다.

3. **collect**: 정렬된 결과를 Collectors.toList() 함수를 이용하여 List 형태로 반환한다.

   

이번에 경험한 상황은 해당 DTO의 멤버변수로 또 DTO가 있고, 멤버변수DTO 안의 멤버변수를 기준으로 정렬하는 것이었다. 

쉽게 말해 selected DTO의 count를 기준으로 정렬하는 것이 문제였다.

```java
class MyDTO {
String id;
Long count;
MyDTO selectedDTO;
    (...)
}
```



## 해결 방법

Comparator class의 comparing을 그대로 사용해줄 수 없으므로 비교작업에 대한 customizing이 필요하다. 이런 상황에서는 두 가지 방법이 있다.

1. MyDTO가 Comparable interface를 implements 한후 comareTo 함수를 override하도록 만든다.

   ```java
   class MyDTO implements Comparable {
   (...)
       @Override
       int compareTo(T o){
            if (this.getSelectedMyDTO() == null && o.getSelectedMyDTO() != null)
                           return -1;
            if (this.getSelectedMyDTO() != null && o.getSelectedMyDTO() == null)
                           return 1;
            if (this.getSelectedMyDTO() == null && o.getSelectedMyDTO() == null)
                           return o1.getCount().compareTo(o2.getCount());
   
            return this.getSelectedMyDTO().getCount()
                           .compareTo(o2.getSelectedMyDTO().getCount());
       }
   }
   
   사용방법:
   List<MyDTO> myDTOs;
   Collections.sort(myDTOs);
   ```

2.  Custom Comparater를 구현하고 Comparator.comparing 함수 대신 구현한 Comparator를 넣어준다.   

    ```java
    Comparator<MyDTO> comparator = new Comparator<MyDTO>() {
            @Override
            public int compare(MyDTO o1, MyDTO o2) {
                  if (o1.getSelectedMyDTO() == null && o2.getSelectedMyDTO() != null)
                            return -1;
                  if (o1.getSelectedMyDTO() != null && o2.getSelectedMyDTO() == null)
                            return 1;
                  if (o1.getSelectedMyDTO() == null && o2.getSelectedMyDTO() == null)
                            return o1.getCount().compareTo(o2.getCount());

                  return o1.getSelectedMyDTO().getCount()
                            .compareTo(o2.getSelectedMyDTO().getCount());
            }
    };
    사용방법: 
     .sorted(comparator.reversed())
    ```



override해서 구현할 때는 다음의 내용을 기억하면 된다. 

| 리턴값           | 왼쪽의 오브젝트가                       |
| :--------------- | --------------------------------------- |
| negative integer | less than **오른쪽 오브젝트**           |
| zero             | equal to **오른쪽 오브젝트selectedDTO** |
| positive integer | greater than **오른쪽 오브젝트**        |



## 결론

DTO의 멤버변수가 자기 자신일 경우에는, compareTo 함수를 override하면 compareTo에서 selectedDTO를 참조하여 compareTo를 보내면 자바는 selectedDTO의 compareTo 함수를 다시 한번 호출하게 되므로 null pointer exception이 발생할수 밖에 없다. 그러므로 이 문제 상황에서 1번은 적절한 방법이 아니다.



> 1번의  Comparable interface를 구현하는 방법을 사용할 때는 

해당 DTO의 비교 방법이 모든 상황에 동일하고, 멤버변수가 자기 자신이 아닌 경우에 사용하면 좋다.

> 2번의  Comparator를 구현하는 방법을 사용할 때는 

정렬의 기준이 되는 멤버변수가 상황에 따라 다양한 경우, 멤버변수가 자기 자신인 경우에 사용하면 좋다. 
