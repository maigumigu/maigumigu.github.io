---
title: "IntelliJ 개꿀기능(1) -클래스 다이어그램 그리는 법"
date: 2019-12-02 10:34:28 -0400
categories: IntelliJ
---

학교나 회사에서 설계보고서를 작성할 때 가끔씩 클래스 다이어그램을 그릴 일이 있다. 

개인적으론 Eclipse의 다이어그램 그리는 기능이 불편했다. 

그런데 이번에 IntelliJ의 기능을 체험하고 사용성이 너무 좋다고 느껴서 호다닥 포스팅을 하러 왔다.

샘플 프로젝트는 내 토이 프로젝트를 사용하였다.





## 1. 다이어그램 그리기



다이어그램을 그리고 싶은 클래스에서 오른쪽 마우스 클릭한다.

여러개 클래스를 선택해서 한 캔버스 안에 그릴 수도 있다. 나는 하나만 선택하였다.

![img](https://lh5.googleusercontent.com/l9dTMXw4cHfNt7jTSnBXmfHu6KGQLMQFlg4mo3Ex43qaikZJ4hJbF1G1YrfmhJ-wPRwtJzEH6ukxXjvzlHy-XNy_prZZePfpabp1yH2X1nO4550lUCwqlEnQ2bErai97LNIs3mTy)





다이어그램 타입에서 Class Diagrams 선택하기.

![img](https://lh6.googleusercontent.com/-cy867ppBNLRa790vBQtQitZ2HrQuw10kZq9hEQrVq_2UEtoC5m9JYx-6fqL33oka35KU7VOvuyNJjA_0n8esrmrRWdCYqanQuK5J9eckVHreehz5hniRH_g6acc-SCMnKVcr4DO)









## 2. 다이어그램 결과





UnivRankTsv가 BasicTsvParser를 상속 받고 있으므로 실선 화살표가 위로 향해 있다.

우리가 일반적으로 아는 클래스 다이어그램의 모양은 아닌 것을 알 수 있다. 



![img](https://lh5.googleusercontent.com/9vDHiYphzJf2eBKIoXtypbIGNC88obcrmLdc5OnT0HKCCpMowrYuRueYowqjE0i2e2ZRRuVEkHNOC5qZSaX1KJ3KteE7Z3sP-O6mbO4bQi_fYr4vq4mcRKqXOt095WrMOeYWzVMy)

이 때 위의 메뉴에 다음과 같은 인터페이스들이 존재한다. 표시하고 싶은 클래스 요소들을 선택할 수도 있는 것이다!

![img](https://lh3.googleusercontent.com/xm1fN5YVKeCR3CwEk1J72GbShodkyLbKu9og_zEm5iXRphV6hhY1c07Jz2Jj-DPMkXQ7CLVci2fc_JhfkxUkFctdL2piz7K55RRYj_LgGC7ZlBv9uRjg7TRxIEygmoAajS2mhtPS)

- f : 필드

- m★: 생성자

- m: 메소드

- p :프로퍼티

- i : 이너 클래스


다음은 필드와 메소드를 선택하고 인터페이스에서 링크를 선택한 결과이다.





![img](https://lh5.googleusercontent.com/KKccOm_bBOOSRT98XphPnN4TNAclvEjZjQ0YFITnHLg_DUE271qLcSwQOVdFm_b3VeiYaJobIDv6l5lIvWsxMYOHTWHrdcpqxP4cnxsUQY6S4KOO8K43aU6hdjBir-MY3H5rLFR-)

UnivRankTsvParser와 상속관계는 아니지만 멤버 함수들의 파라미터인 'ArrayList<UnivRankDTO>' 를 추가할 수도 있다. 스페이스바 누르고 클래스 이름 입력하면 끄읕!

 

![img](https://lh3.googleusercontent.com/YcMH0VTpkD2LZsfS1EZgyIFyw5AaYVvgYyzjlPH1n2OvLcglDDdjsnjgjmVaSTVog2IG4lW-aKSV8t1PzmH8grtyt4w3YIt9nm9eB4BgNQAYGEfe5kT0iQ-1fTygNvypQQ89dwVo)



UnivRankDTO 추가한 결과.





![img](https://lh3.googleusercontent.com/6e_Ki1rQZc_vaC6X1PPF3Vvl3Jo9gEz6riA8CdjA3vWYjG5zt98IN7SuOiBcW25ycO64VjfTpNhS27OiaYru-ISfX04QgKCphwQit5XWlGgcHVNZ3xpX-7JPzGodHC5hTy8JY5hT)





## 3. 다이어그램 원하는 모양으로 만들기





사실 내가 가장 좋았던 건 다이어그램의 레이아웃을 자유자재로 변경할 수 있다는 것이었다.

오른쪽 마우스 메뉴의 Layout에서 원하는 레이아웃을 선택하면 애니메이션과 함께 레이아웃이 변경된다.





![img](https://lh5.googleusercontent.com/nzJYKWsjFwZAX28ghi7NFtGavr1gdDVky_iZDKB5YK0S7UcFwyGAPaO9ebzA651XL2VFVKNF1GXxnV4UoCC60ZbbrOrMOM4eO42ohBaFmPR9-JfMBa7iazWaS2mg8IhB-2_Kd5UK)

애니메이션이 궁금한 사람은 아래 링크 클릭!↓

http://youtu.be/eOoyHBOZWvo



## 결론

설계 보고서를 작성할 때 IDE로 IntelliJ를 사용했다면 클래스 다이어그램 걱정은 끝난 것이나 다름없다 ^.^

여러분 모두 IntelliJ 하세요~(학생은 1년 무료)

