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
## 와일드카드를 이용한 타입 한정
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
제네릭을 사용할 때 알아두면 좋은 법칙
### PE - Producer Extends
- 외부로부터 받아온 제네릭 매개변수로부터 데이터를 얻어오는 경우 외부 제네릭의 레퍼런스를 `<? extends T>`로 한정
- extends 키워드를 사용해야 T 타입의 객체를 반환할 수 있음이 보장
### CS - Consumer Super
- 외부 제네릭 매개변수에다가 데이터를 추가하는 경우 외부 제네릭의 레퍼런스를 `<? super T>`로 한정
- super 키워드를 사용해야 T 타입의 객체를 인자로 전달할 수 있음이 보장
## PECS가 필요한 이유 알아보기
제네릭 클래스 내 메서드의 인자값, 반환값에 대해 알아보고자 함
![제네릭변수 이용 방법 고찰](wildcard.png)

위의 클래스 구조를 이용한 리스트에서 원소를 꺼내고 넣으려면 어떻게 해야 할지 살펴봄
### `List<? extends A> items`
- `items`에 할당할 수 있는 객체는 `List<A>`부터 `List<E>`까지 모두 가능
- `items.get()` - A 타입으로만 꺼내야 안전. 하위 타입으로 캐스팅이 안될 수 있음
- `items.add()` - null만 저장 가능
  > - `<? extends A>`라는 표현식은 A의 서브타입을 다룸
  > - 하지만 컴파일러 입장에서 어떤 서브타입이 전달되었는지 명시된 바가 없음
  > - 따라서 컴파일러는 표현식 `<? extends A>`을 *평가*할 때 A에 대한 임의의 서브타입을 생성하여 타입 파라미터에 대입
  >   - B~E에 해당하지 않는, `capture #n of ? ~~` 형태의 클래스로 간주하며 컴파일러 내부에서만 이용됨
  >   - 이를 *와일드카드 캡처*라고 함
  > - 위에서 언급한 임의의 서브타입을 X라고 할 때, `List<X>.add()`에는 X의 서브타입만을 인자로 허용
  > - 그러나 위의 클래스 구조에서 X의 서브타입이 없으므로 `add()`의 인자로 **null만 전달 가능**
   ```java
   List<? extends A> items = new ArrayList<B>();
  
   // items.add(new C()); // ArrayList<B>의 입장에서 C는 B 타입이 아니므로 컴파일 오류인게 납득됨
   // items.add(new B()); // ArrayList<B>와 동일한 타입이라 될 것 같지만.. 컴파일 오류

   items = new ArrayList<A>();
   // items.add(new A()); // 마찬가지로 오류
   items1.add(null); // 유일하게 null만 가능
   ```  
### `List<? super A> items`
- `items`에 할당할 수 있는 객체는 `List<Object>`, `List<A>`
- `items.get()` - `Object`로 꺼내야 안전. A를 포함한 하위 타입으로 캐스팅 안될 수 있음
- `items.add()` - A의 모든 하위 타입 추가 가능. 상위 타입 및 그 외 타입 추가시 런타임 예외 발생
  > - `<? super A>` 라는 표현식은 컴파일러 입장에서 A의 어떤 조상이 타입 파라미터로 전달될지 모름
  >   - extends와 마찬가지로 *와일드카드 캡처*를 이용해 표현식 판단
  > - 타입 파라미터에 A의 임의의 조상 X가 전달되므로 아래의 사항 성립
  >   1. 메서드의 제네릭 타입 반환값을 X의 유일한 조상 `Object`만 허용
  >   2. 메서드의 제네릭 타입 인자로 최소한 A 타입의 객체 허용
  ```java
  List<? super A> items = new ArrayList<Object>();
  
  items.add(new C());
  items.add(new D());

  // items.add(new Object()); // A의 하위 타입이 아니므로 런타임 익셉션 발생 가능성 존재
  ```