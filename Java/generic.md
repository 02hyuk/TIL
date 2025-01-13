# 제네릭
- 클래스나 메서드 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법
- 타입 파라미터에 다형성이 적용되어 특정 타입으로 제네릭 구체화 이후 서브클래스를 통해서 조작 가능
  - 아래 예시에서 타입 파라미터로 Number가 전달되면 생성자의 인자로 Integer 전달 가능
- 제네릭 정의 부분에서 타입 파라미터에 대한 객체 생성 불가(`T t1 = new T();` 처럼 T 뒤에 new 키워드 사용 못함)
  - 생성자로 사용된 타입을 컴파일 타임에 알 수 없으므로 해당 생성자가 올바른 문법인지 판단할 수 없기 때문이 아닐까?
- static 멤버에 제네릭이 올 수 없음
  - 제네릭 객체가 생성되기도 전에 static 멤버가 정해져야 하는 상황에서(static 특징) 제네릭 타입을 특정지을 수 없기 때문
- 제네릭 타입 배열 문법
  - `Example<Object>[] examples = new Example[10];` - `Example<Object>` 타입을 저장하는 길이 10 배열 생성
  ```java
  // MyGeneric 정의부에서 타입 파라미터 T 사용
  public class MyGeneric<T> {
      T data;

      public MyGeneric(T data) {
          this.data = data;
      }

      public T getData() {
          return data;
      }
  }

  // main() 내부
  // 외부에서 제네릭 클래스를 사용할 때 타입 매개변수에 전달하는 타입 통해 제네릭 구체화 - 전달되는 타입이 T에 대입
  MyGeneric<Integer> obj = new MyGeneric<Integer>(3); // 생성자에도 타입 구체화하는 것이 원칙
  // 레퍼런스에서 구체화했으므로 컴파일러가 생성자의 타입 파라미터 추론 가능 - 버전 1.7 이후
  MyGeneric<String> obj2 = new MyGeneric<>("thisisneverthat"); 
  ```
## 타입 한정
- 제네릭을 정의할 때 전달받을 수 있는 타입 파라미터의 조건을 정할 수 있음
- `extends` 키워드: 사용 가능한 타입의 **상한**을 지정하는 데 사용
  - `<T extends A>`의 경우 타입 파라미터 T의 조건으로 A, 또는 A를 무조건 상속/구현해야 함을 지정
- `super` 키워드: 사용 가능한 타입의 **하한**을 지정하는 데 사용
  - `<T super B>`의 경우 T의 조건으로 B 자신을 포함해 B의 조상을 지정
- extends, super 다음에 클래스뿐만 아니라 인터페이스도 올 수 있음
- 다중 타입 한정: `&` 사용해 조건으로 지정할 타입 열거
### 재귀적 타입 한정
- 클래스 정의부에서 자기 자신이 들어간 표현식을 사용해 재귀적으로 타입 조건을 한정하는 것

## 제네릭 메서드
- 타입 파라미터를 이용해 메서드에 사용되는 타입을 지정할 수 있는 메서드
    ```java
    public static <T> void myMethod(T a1, T a2){
        // ...
    }
    // 또는
    public <T> T getData(); 
    ```
- 메서드 선언부에 <> 연산자가 사용되며, 제네릭 클래스의 <> 연산자와는 독립적으로 작동
    > 제네릭 클래스 안에 제네릭 메서드가 정의된 경우, 
제네릭 메서드의 <> 연산자에 해당하는 타입은 기본적으로 클래스의 <> 연산자를 따라가나, 
클래스와는 독립적으로 타입을 정해줄 수 있음
### 제네릭 메서드 호출
- 원칙적으로는 메서드 이름 바로 왼쪽에 <>를 통해 타입 파라미터 전달
- 컴파일러의 파라미터 타입 추론 기능으로 <>을 사용하지 않아도 제네릭 메서드 호출 가능
    ```java
    public class MyClass {
        
        // ...
  
        public static <T> T doMyGeneric(T a, T b){
            // ...
        }
        
        public static void main(String[] args) {
            // 원칙 - 파라미터 직접 지정
            String s = MyClass.<String>doMyGeneric("abc", "xyz");
            
            // 컴파일러는 파라미터 타입을 추론할 수 있음
            int num = MyClass.doMyGeneric(1, 3);
        }
    }
    ```