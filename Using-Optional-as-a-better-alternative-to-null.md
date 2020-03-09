# 1️⃣ 값이 없는 상황을 어떻게 처리할까?

## 1. 보수적인 자세로 NullPointerException 줄이기

- 모든 변수가 null인지를 의심
    1. 변수를 접근할 때마다 중첩된 if문 추가
        - 코드 들여쓰기 수준 증가
            - 반복패턴 구조 형성
                
            → 이를 깊은 의심(deep doubt) 이라고 부름
    2. if를 통해 확인 후 출구 생성
        - 출구에서 반환되는 값의 일관성 유지가 어려움

> ⚠ 즉, 최대한 보수적으로 작성해도 null일 수 있다는 사실을 까먹게 된다면, 언제 어디서 NPE이 발생할 지 모른다.

## 2. null 때문에 발생하는 문제

- **에러의 근원**

    NPE는 자바에서 가장 흔히 발생하는 에러다.

- **코드를 어지럽힘**

    때로는 중첩된 null 확인 코드를 추가해야 하므로 null 때문에 코드 가독성이 떨어진다.

- **아무 의미가 없음**

    null은 아무 의미도 표현하지 않는다. 

- **자바 철학에 위배**

    자바는 개발자로부터 모든 포인터를 숨겼다. 하지만 null 포인터라는 예외가 있다.

- **형식 시스템에 구멍을 만듦**

    null은 무형식이며 정보를 포함하고 있지 않으므로 모든 참조 형식에 null을 할당할 수 있다. 이런 식으로 null이 할당되기 시작하면서 시스템의 다른 부분으로 null이 퍼졌을 때 애초에 null이 어떤 의미로 사용되었는지 알 수 없다. 

## 3. 다른 언어는 null 대신 무얼 사용하나?

- 그루비 : 안전 네비게이션 연산자(.?)
- 하스켈 : 선택형 값 Maybe - 주어진 형식의 값을 갖거나 아니면 아무 값도 갖지 않을 수 있다.
- 스칼라 : Option[T] - T 형식의 값을 갖거나 아무 값도 갖지 않을 수 있다.

# 2️⃣ Optional 클래스 소개

## Optinal

선택형 값을 캡슐화하는 클래스

- 값 존재 : 값을 감싼다
- 값 부재 :  싱글턴 인스턴스를 반환하는 정적 팩토리 메서드인 Optioanl.empty를 통해 optional 반환

## Optinal과 null의 차이점

- null을 참조하려 하면 NPE가 발생
- Optional.empty는 Optional 객체이기 때문에 다양하게 활용 가능
    - Optional을 이용하면 값이 없는 상황이 우리 테이터에 문제가 있는 것인지 아니면 알고리즘의 버그인지 명확하게 구분할 수 있다.

# 3️⃣ Optional 적용 패턴

## 1. Optional 객체 만들기

### 빈 Optional

*Optional<Car> optCar = Optional.empty();*

### null이 아닌 값으로 Optional 만들기

*Optional<Car> optCar = Optional.of(car);*

### null값으로 Optional 만들기

*Optional<Car> optCar = Optional.ofNullable(car);*

## 2. 맵으로 Optional의 값을 추출하고 변환하기

 Optional을 최대 요소의 갯수가 1 이하인 컬렉션으로 생각하고 map을 적용하자

## 3. flatMap으로 Optional 객체 연결

 함수를 적용하여 생성된 모든 스트림이 하나의 스트림으로 병합되어 평준화된다.

- 평준화 과정 : 이론적으로 두 Optional을 합치는 기능을 수행하면서 둘 중 하나라도 null이면 빈 Optional을 생성하는 연산
- Optional 클래스는 필드 형식으로 사용할 것을 가정하지 않았기 때문에 Serializable 인터페이스를 구현하지 않는다.

    만약, 직렬화 모델이 필요하다면 Optional로 값을 반환받을 수 있는 메서드를 추가하는 방식으로 구현하자. 

## 4. Optional 스트림 조작

Optional 클래스의 stream() 메서드

: 각 Optional이 비어있는지 아닌지에 따라 Optional을 0개 이상의 항목을 포함하는 스트림으로 반환

## 5. 디폴트 액션과 Optional 언랩

- **get()**

    래핑된 값이 있으면 해당 값 반환, 없으면 NoSuchElementException 발생

    → 중첩된 null 확인 코드를 넣는 상황과 크게 다르지 않다.

- **orElse(T other)**

    Optional이 값을 포함하지 않을 때 기본값을 제공할 수 있다.

- **orElseGet()**

    orElse 메서드에 대응하는 게으른 버전

    Optional에 값이 없을 때만 Supplier실행


- **orElseThrow()**

    Optional이 비었을 때 예외를 발생시킴(get()과 비슷)

    발생시킬 예외의 종류를 선택 할 수 있음

- **ifPresent()**

    값이 존재할 때 인수로 넘겨준 동작을 실행

    값이 없으면 아무 일도 일어나지 않음

- **ifPresentofElse()**

    값이 존재할 때 인수로 넘겨준 동작을 실행 

    값이 없으면 실행할 수 있는 Runnable을 인수로 받는다. 


# 4️⃣ Optional을 사용한 실용 예제

## 1. 잠재적으로 null이 될 수 있는 대상을 Optional로 감싸기

기존 null을 반환하는 코드를 Optional로 감싼다

- 기존 *Object value = map.get("key");*

- 수정 *Optional<Object> value = Optional.ofNullable(map.get("key"));*

## 2. 예외와 Optional 클래스

parseInt와 같이 null대신 예외를 발생시키는 함수는 OptionalUtility를 만든다. 그안에 메소드를 try-catch로 구현하여 optional을 반환하도록 한다. 

## 3. 기본형 Optional을 사용하지 말하야 하는 이유

Optional스트림의 최대 요소 수는 1개이므로 기본형 특화 클래스로 성능을 개선 할 수 없다. 또한 클래스의 map, flatMap, filter등을 지원하지 않는다. 