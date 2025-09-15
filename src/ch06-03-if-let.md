## `if let`과 `let else`로 간결한 제어 흐름

`if let` 문법은 `if`와 `let`을 결합하여, 한 패턴에만 일치하는 값을 처리하고 나머지는 무시하는 간결한 방법을 제공합니다. 아래 Listing 6-6의 프로그램은 `config_max` 변수에 들어 있는 `Option<u8>` 값을 매칭하지만, `Some` 변형일 때만 코드를 실행하고 싶을 때 사용합니다.

<Listing number="6-6" caption="값이 `Some`일 때만 코드를 실행하는 `match`">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-06/src/main.rs:here}}
```

</Listing>

값이 `Some`이면, 패턴에서 `max` 변수에 값을 바인딩하여 `Some` 변형 안의 값을 출력합니다. `None` 값에는 아무 동작도 원하지 않습니다. 하지만 `match` 표현식의 요구를 만족하려면, 한 변형만 처리한 뒤에도 `_ => ()`를 추가해야 하므로, 불필요한 보일러플레이트 코드가 생깁니다.

대신, 아래처럼 더 짧게 `if let`을 사용할 수 있습니다. 아래 코드는 Listing 6-6의 `match`와 동일하게 동작합니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-12-if-let/src/main.rs:here}}
```

`if let` 문법은 패턴과 표현식을 등호로 구분합니다. 동작 방식은 `match`와 같으며, 표현식이 `match`에 들어가고 패턴이 첫 번째 팔이 됩니다. 여기서는 패턴이 `Some(max)`이고, `max`가 `Some` 안의 값을 바인딩합니다. 이후 `if let` 블록에서 `max`를 사용할 수 있습니다. 블록의 코드는 값이 패턴과 일치할 때만 실행됩니다.

`if let`을 사용하면 입력이 줄고, 들여쓰기와 보일러플레이트 코드가 줄어듭니다. 하지만 `match`가 강제하는 완전성 검사(exhaustiveness)는 사라집니다. 즉, 모든 경우를 처리하지 않아도 되므로, 상황에 따라 간결함과 안전성 사이에서 선택해야 합니다.

즉, `if let`은 한 패턴에만 일치하는 코드를 실행하고, 나머지는 무시하는 `match`의 문법 설탕(syntax sugar)이라고 생각할 수 있습니다.

`if let`에는 `else`를 함께 사용할 수 있습니다. `else` 블록의 코드는, `match`에서 `_` 패턴에 해당하는 코드와 동일하게 동작합니다. Listing 6-4에서 `Quarter` 변형에 `UsState` 값을 담은 `Coin` 열거형을 정의한 것을 기억하세요. 쿼터가 아닌 동전을 세면서, 쿼터의 주 이름을 출력하고 싶다면, 아래처럼 `match`로 처리할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-13-count-and-announce-match/src/main.rs:here}}
```

또는 아래처럼 `if let`과 `else`로 처리할 수도 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-14-count-and-announce-if-let-else/src/main.rs:here}}
```

## `let...else`로 "행복한 경로" 유지하기

자주 쓰는 패턴 중 하나는, 값이 있으면 어떤 작업을 수행하고, 없으면 기본값을 반환하는 것입니다. 예를 들어, 쿼터에 담긴 주의 나이에 따라 재미있는 메시지를 출력하고 싶다면, 아래처럼 `UsState`에 주의 나이를 확인하는 메서드를 추가할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-07/src/main.rs:state}}
```

그 다음, 아래 Listing 6-7처럼 `if let`을 사용해 코인 타입을 매칭하고, 조건문 내부에서 `state` 변수를 도입할 수 있습니다.

<Listing number="6-7" caption="`if let` 내부에서 조건문을 중첩해 1900년에 존재했던 주인지 확인하기">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-07/src/main.rs:describe}}
```

</Listing>

이 방식도 동작하지만, 모든 작업이 `if let` 블록 내부로 들어가므로, 작업이 복잡해지면 상위 분기 구조를 파악하기 어려워질 수 있습니다. 또, 표현식이 값을 반환한다는 점을 활용해, `if let`에서 값을 만들거나, 일치하지 않으면 바로 반환하는 방식도 사용할 수 있습니다(아래 Listing 6-8). `match`에서도 비슷하게 할 수 있습니다.

<Listing number="6-8" caption="`if let`으로 값을 만들거나 바로 반환하는 예시">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-08/src/main.rs:describe}}
```

</Listing>

하지만 이 방식도 한쪽 분기는 값을 만들고, 다른 쪽은 함수 전체를 반환하므로, 흐름을 따라가기 다소 불편합니다.

이런 흔한 패턴을 더 명확하게 표현하기 위해, 러스트에는 `let...else` 문법이 있습니다. `let...else`는 왼쪽에 패턴, 오른쪽에 표현식을 두며, `if let`과 비슷하지만 `if` 분기가 없고, 오직 `else` 분기만 있습니다. 패턴이 일치하면, 패턴에서 바인딩한 값이 바깥 스코프에 남습니다. 패턴이 일치하지 않으면, `else` 블록으로 흐름이 넘어가며, 반드시 함수에서 반환해야 합니다.

아래 Listing 6-9는 Listing 6-8의 코드를 `let...else`로 바꾼 예시입니다.

<Listing number="6-9" caption="`let...else`로 함수 흐름을 더 명확하게 표현하기">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-09/src/main.rs:describe}}
```

</Listing>

이 방식은 함수의 "행복한 경로(happy path)"를 메인 본문에 유지하면서, 두 분기에서 흐름이 크게 달라지지 않게 해줍니다.

`match`로 표현하기 너무 장황한 로직이 있다면, `if let`과 `let...else`도 러스트의 도구 상자에 있다는 점을 기억하세요.

## 요약

이제 열거형을 사용해 여러 값을 하나의 타입으로 묶는 방법을 배웠습니다. 표준 라이브러리의 `Option<T>` 타입이 타입 시스템을 활용해 오류를 방지하는 데 어떻게 도움이 되는지도 살펴봤습니다. 열거형 값에 데이터가 들어 있으면, 처리할 경우의 수에 따라 `match`나 `if let`으로 값을 꺼내 사용할 수 있습니다.

이제 러스트 프로그램에서 구조체와 열거형을 활용해 도메인 개념을 표현할 수 있습니다. API에 맞는 사용자 정의 타입을 만들면 타입 안전성이 보장되며, 컴파일러가 각 함수에 올바른 타입의 값만 전달되도록 확인해 줍니다.

사용자에게 명확하고 필요한 기능만 노출하는 잘 구성된 API를 제공하려면, 이제 러스트의 모듈 시스템을 배워봅시다.