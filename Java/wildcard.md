# 와일드카드
- 제네릭 타입을 외부에서 이용할 때, 타입 파라미터에 사용할 수 있는 `?` 기호
- 와일드카드를 이용하면 해당 자리에는 어떤 타입이든 전달 가능
  ```java
  public void loopData(List<? extends Number> data) {}
  ```
## 상하위 타입 및 변성
- 변성: 타입의 상속 계층 관계에서 서로 다른 타입 간에 어떤 관계가 있는지를 나타내는 지표
- 공변성: `S`가 `T`의 하위 타입일 때 어느 클래스 `C<S>`가 `C<T>`의 하위 타입인 경우를 나타내는 단어
- 반공변성: `S`가 `T`의 하위 타입일 때 `C<T>`가 `C<S>`의 하위 타입인 경우를 나타내는 단어(공변성과 반대)
- 무공변성: `S`가 `T`의 하위 타입이더라도 `C<S>`와 `C<T>`는 서로 관련이 없음을 의미하는 단어
### 자바에서의 변성 예시
- 공변성: 배열
    - `S`가 `T`의 하위 타입일 때 `S[]`가 `T[]`의 하위 타입
- 무공변성: 제네릭
    - `S`가 `T`의 하위 타입이더라도 `List<S>`와 `List<T>`는 서로 업/다운 캐스팅 불가
## 와일드카드에서의 타입 한정
- 공변성: `S`가 `T`의 하위 타입일 때, `List<S>`는 `List<? extends T>`의 하위 타입
- 반공변성: `S`가 `T`의 하위 타입일 때, `List<T>`는 `List<? super S>`의 하위 타입
  ```java
  // 공변성
  ArrayList<? extends Number> list = new ArrayList<Integer>();
  
  // 반공변성
  // Class2가 Class1의 하위 타입인 경우
  ArrayList<? super Class2> list2 = new ArrayList<Class1>();
  ```
  > 와일드카드 `?`는 `? extends Object`와 같은 의미
  > - 타입 파라미터에 `Object`를 포함한 모든 타입을 대입할 수 있기 때문
## PECS 법칙
### PE - Producer Extends
- 외부로부터 받아온 제네릭 매개변수로부터 데이터를 얻어오는 경우 외부 제네릭의 레퍼런스를 `<? extends T>`로 한정
### CS - Consumer Super
- 외부 제네릭 매개변수에다가 데이터를 추가하는 경우 외부 제네릭의 레퍼런스를 `<? super T>`로 한정