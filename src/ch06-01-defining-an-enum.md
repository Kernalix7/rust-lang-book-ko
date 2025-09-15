## 열거형(Enum) 정의하기

구조체(struct)는 관련된 필드와 데이터를 함께 묶는 방법을 제공하지만, 열거형(enum)은 값이 여러 가능한 값 중 하나임을 표현할 수 있게 해줍니다. 예를 들어, `Rectangle`이 `width`와 `height`를 가진 도형이라면, `Rectangle`이 `Circle`이나 `Triangle` 등 여러 도형 중 하나임을 나타내고 싶을 수 있습니다. 러스트에서는 이러한 가능성을 열거형으로 표현할 수 있습니다.

코드로 표현하고 싶은 상황을 살펴보면서 열거형이 왜 유용하고, 이 경우 구조체보다 더 적합한지 알아봅시다. 예를 들어, IP 주소를 다뤄야 한다고 가정해봅시다. 현재 IP 주소에는 두 가지 주요 표준이 있습니다: 버전 4와 버전 6입니다. 우리 프로그램이 접하게 될 IP 주소는 이 두 가지뿐이므로, 모든 가능한 경우를 _열거_할 수 있습니다. 이것이 열거형(enumeration)의 이름이 유래된 이유입니다.

IP 주소는 버전 4 또는 버전 6 중 하나일 수 있지만, 동시에 둘 다일 수는 없습니다. 이러한 특성 때문에 열거형 데이터 구조가 적합합니다. 열거형 값은 오직 하나의 변이(variant)만 가질 수 있기 때문입니다. 버전 4와 버전 6 주소 모두 근본적으로 IP 주소이므로, 코드에서 IP 주소와 관련된 상황을 처리할 때는 동일한 타입으로 다루는 것이 좋습니다.

이 개념을 코드로 표현하기 위해 `IpAddrKind`라는 열거형을 정의하고, IP 주소가 될 수 있는 가능한 종류인 `V4`와 `V6`를 나열할 수 있습니다. 이것이 열거형의 변이(variant)입니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:def}}
```

이제 `IpAddrKind`는 우리가 코드에서 사용할 수 있는 사용자 정의 데이터 타입이 되었습니다.

### 열거형 값

다음과 같이 `IpAddrKind`의 두 변이 각각의 인스턴스를 만들 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:instance}}
```

열거형의 변이는 해당 식별자 아래에 네임스페이스로 묶여 있으며, 두 개의 콜론을 사용해 구분합니다. 덕분에 `IpAddrKind::V4`와 `IpAddrKind::V6` 두 값 모두 동일한 타입인 `IpAddrKind`가 됩니다. 예를 들어, 어떤 `IpAddrKind`든 받을 수 있는 함수를 정의할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn}}
```

그리고 이 함수는 두 변이 모두로 호출할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn_call}}
```

열거형을 사용하면 더 많은 장점이 있습니다. IP 주소 타입에 대해 더 생각해보면, 현재 우리는 실제 IP 주소 _데이터_를 저장할 방법이 없습니다. 단지 어떤 _종류_인지만 알 수 있을 뿐입니다. 5장에서 구조체를 배웠으니, 아래 Listing 6-1처럼 구조체로 이 문제를 해결하고 싶어질 수도 있습니다.

<Listing number="6-1" caption="IP 주소의 데이터와 `IpAddrKind` 변이를 구조체로 저장하기">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-01/src/main.rs:here}}
```

</Listing>

여기서는 `IpAddr`라는 구조체를 정의하고, 두 개의 필드를 가집니다: 이전에 정의한 열거형인 `IpAddrKind` 타입의 `kind` 필드와, `String` 타입의 `address` 필드입니다. 이 구조체의 인스턴스는 두 개입니다. 첫 번째는 `home`이고, `kind` 필드에는 `IpAddrKind::V4` 값이, 주소 데이터에는 `127.0.0.1`이 들어갑니다. 두 번째 인스턴스는 `loopback`이고, `kind` 필드에는 다른 변이인 `V6` 값이, 주소에는 `::1`이 들어갑니다. 구조체를 사용해 `kind`와 `address` 값을 함께 묶었으므로, 변이와 값이 연결됩니다.

하지만 같은 개념을 열거형만으로 표현하면 더 간결해집니다. 구조체 안에 열거형을 넣는 대신, 각 열거형 변이에 데이터를 직접 넣을 수 있습니다. 아래의 새로운 `IpAddr` 열거형 정의는 `V4`와 `V6` 변이 모두에 `String` 값을 연결합니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-02-enum-with-data/src/main.rs:here}}
```

각 열거형 변이에 데이터를 직접 연결하므로, 별도의 구조체가 필요하지 않습니다. 여기서 열거형의 또 다른 특징을 쉽게 볼 수 있습니다: 우리가 정의한 각 열거형 변이의 이름은 해당 열거형 인스턴스를 생성하는 함수가 되기도 합니다. 즉, `IpAddr::V4()`는 `String` 인자를 받아 `IpAddr` 타입의 인스턴스를 반환하는 함수 호출입니다. 열거형을 정의하면 이런 생성자 함수가 자동으로 만들어집니다.

열거형을 사용하면 구조체보다 더 유리한 점이 있습니다: 각 변이에 서로 다른 타입과 개수의 데이터를 연결할 수 있다는 점입니다. 버전 4 IP 주소는 항상 0~255 사이의 네 개의 숫자 요소를 가집니다. 만약 `V4` 주소를 네 개의 `u8` 값으로 저장하고, `V6` 주소는 하나의 `String` 값으로 표현하고 싶다면, 구조체로는 불가능하지만 열거형으로는 쉽게 처리할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-03-variants-with-different-data/src/main.rs:here}}
```

버전 4와 버전 6 IP 주소를 저장하고, 종류를 표현하는 여러 방법을 살펴보았습니다. 실제로 IP 주소를 저장하고 종류를 표현하는 일이 매우 흔하기 때문에 [표준 라이브러리에도 정의가 이미 있습니다!][IpAddr]<!-- ignore --> 표준 라이브러리의 `IpAddr` 정의를 살펴보면, 우리가 만든 열거형과 변이와 거의 동일하지만, 변이 안에 주소 데이터를 두 개의 서로 다른 구조체로 넣었습니다. 각 변이마다 구조체 정의가 다릅니다:

```rust
struct Ipv4Addr {
    // --snip--
}

struct Ipv6Addr {
    // --snip--
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

이 코드는 열거형 변이 안에 어떤 종류의 데이터든 넣을 수 있음을 보여줍니다: 문자열, 숫자 타입, 구조체 등 모두 가능합니다. 심지어 다른 열거형도 포함할 수 있습니다! 표준 라이브러리 타입도 여러분이 직접 만든 것과 크게 다르지 않은 경우가 많습니다.

표준 라이브러리에 `IpAddr` 정의가 있더라도, 우리가 직접 정의한 타입을 문제없이 만들고 사용할 수 있습니다. 표준 라이브러리의 정의를 스코프로 가져오지 않았기 때문입니다. 타입을 스코프로 가져오는 방법은 7장에서 더 자세히 다룹니다.

Listing 6-2에서는 또 다른 열거형 예시를 살펴봅니다. 이번에는 변이마다 다양한 타입이 들어갑니다.

<Listing number="6-2" caption="각 변이가 서로 다른 타입과 개수의 값을 저장하는 `Message` 열거형">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-02/src/main.rs:here}}
```

</Listing>

이 열거형에는 네 가지 변이가 있으며, 각각 다른 타입을 가집니다:

- `Quit`: 아무 데이터도 연결되어 있지 않습니다
- `Move`: 구조체처럼 이름이 붙은 필드를 가집니다
- `Write`: 하나의 `String`을 포함합니다
- `ChangeColor`: 세 개의 `i32` 값을 포함합니다

Listing 6-2처럼 여러 변이를 가진 열거형을 정의하는 것은 여러 종류의 구조체를 정의하는 것과 비슷하지만, 열거형은 `struct` 키워드를 사용하지 않고 모든 변이를 하나의 `Message` 타입 아래에 묶습니다. 아래 구조체들은 앞서 정의한 열거형 변이와 동일한 데이터를 담을 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-04-structs-similar-to-message-enum/src/main.rs:here}}
```

하지만 각각의 구조체는 타입이 다르기 때문에, 앞서 Listing 6-2에서 정의한 `Message` 열거형처럼 모든 종류의 메시지를 받을 수 있는 함수를 쉽게 정의할 수 없습니다. 열거형을 사용하면 하나의 타입으로 처리할 수 있습니다.

열거형과 구조체의 또 다른 공통점이 있습니다: 구조체에 `impl`을 사용해 메서드를 정의할 수 있듯, 열거형에도 메서드를 정의할 수 있습니다. 아래는 `Message` 열거형에 정의할 수 있는 `call`이라는 메서드입니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-05-methods-on-enums/src/main.rs:here}}
```

메서드의 본문에서는 `self`를 사용해 메서드를 호출한 값을 가져옵니다. 이 예시에서는 `Message::Write(String::from("hello"))` 값을 가진 변수 `m`을 만들었고, `m.call()`을 실행하면 `call` 메서드의 본문에서 `self`가 바로 그 값이 됩니다.

이번에는 표준 라이브러리에서 매우 흔하고 유용한 또 다른 열거형인 `Option`을 살펴봅시다.

### `Option` 열거형과 널(null) 값보다 뛰어난 장점

이번 절에서는 표준 라이브러리에 정의된 또 다른 열거형인 `Option`을 사례로 살펴봅니다. `Option` 타입은 값이 있을 수도 있고, 없을 수도 있는 매우 흔한 상황을 표현합니다.

예를 들어, 비어 있지 않은 리스트에서 첫 번째 항목을 요청하면 값을 얻을 수 있습니다. 하지만 빈 리스트에서 첫 번째 항목을 요청하면 아무 값도 얻지 못합니다. 타입 시스템으로 이 개념을 표현하면, 컴파일러가 여러분이 처리해야 할 모든 경우를 제대로 처리했는지 확인해줍니다. 이 기능은 다른 프로그래밍 언어에서 매우 흔하게 발생하는 버그를 예방할 수 있습니다.

프로그래밍 언어 설계는 어떤 기능을 포함할지에 초점을 두는 경우가 많지만, 어떤 기능을 제외할지도 중요합니다. 러스트에는 많은 언어에 있는 널(null) 기능이 없습니다. _널_은 값이 없음을 의미하는 값입니다. 널이 있는 언어에서는 변수는 항상 두 가지 상태 중 하나일 수 있습니다: 널 또는 널이 아님.

널의 창시자인 Tony Hoare는 2009년 발표 “Null References: The Billion Dollar Mistake”에서 이렇게 말했습니다:

> 나는 이것을 10억 달러짜리 실수라고 부른다. 당시 나는 객체 지향 언어에서 참조를 위한 최초의 포괄적 타입 시스템을 설계하고 있었다. 내 목표는 모든 참조 사용이 완전히 안전하도록 하고, 컴파일러가 자동으로 검사해주도록 하는 것이었다. 하지만 구현이 너무 쉬워서 널 참조를 넣고 싶은 유혹을 뿌리칠 수 없었다. 그 결과 지난 40년 동안 셀 수 없이 많은 오류, 취약점, 시스템 충돌이 발생했고, 아마도 10억 달러에 달하는 고통과 피해를 초래했을 것이다.

널 값의 문제는 널 값을 널이 아닌 값처럼 사용하려 하면 어떤 종류든 오류가 발생한다는 점입니다. 널 또는 널이 아님이라는 특성이 널리 퍼져 있기 때문에, 이런 실수를 저지르기 매우 쉽습니다.

하지만 널이 표현하려는 개념 자체는 여전히 유용합니다: 널은 현재 유효하지 않거나 어떤 이유로 값이 없는 상태를 나타냅니다.

문제는 개념이 아니라 구현 방식에 있습니다. 그래서 러스트에는 널이 없지만, 값이 있을 수도 없을 수도 있음을 표현할 수 있는 열거형이 있습니다. 이 열거형이 바로 `Option<T>`이며, [표준 라이브러리에서 다음과 같이 정의되어 있습니다][option]<!-- ignore -->:

```rust
enum Option<T> {
    None,
    Some(T),
}
```

`Option<T>` 열거형은 매우 유용하기 때문에 프렐루드(prelude)에 포함되어 있습니다. 별도로 스코프로 가져올 필요가 없습니다. 변이도 프렐루드에 포함되어 있으므로, `Option::` 접두사 없이 `Some`과 `None`을 바로 사용할 수 있습니다. `Option<T>` 열거형도 일반적인 열거형이며, `Some(T)`와 `None`은 모두 `Option<T>` 타입의 변이입니다.

`<T>` 문법은 아직 다루지 않은 러스트의 기능입니다. 이것은 제네릭 타입 매개변수이며, 10장에서 더 자세히 다룹니다. 지금은 `<T>`가 `Option` 열거형의 `Some` 변이가 어떤 타입의 데이터든 하나를 담을 수 있음을 의미한다는 것만 알면 됩니다. 그리고 `T`에 어떤 구체적인 타입이 들어가느냐에 따라 전체 `Option<T>` 타입이 달라집니다. 아래는 `Option` 값을 사용해 숫자 타입과 문자 타입을 저장하는 예시입니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-06-option-examples/src/main.rs:here}}
```

`some_number`의 타입은 `Option<i32>`입니다. `some_char`의 타입은 `Option<char>`이며, 서로 다른 타입입니다. 러스트는 `Some` 변이 안에 값을 명시했으므로 타입을 추론할 수 있습니다. 하지만 `absent_number`처럼 `None` 값만 있으면, 러스트는 전체 `Option` 타입을 명시적으로 지정해야 합니다. 컴파일러가 어떤 타입의 `Some` 변이가 들어올지 추론할 수 없기 때문입니다. 여기서는 `absent_number`가 `Option<i32>` 타입임을 러스트에 알려줍니다.

`Some` 값을 가지면 값이 존재하며, 그 값은 `Some` 안에 들어 있습니다. `None` 값을 가지면, 널과 비슷하게 유효한 값이 없다는 뜻입니다. 그렇다면 `Option<T>`가 널보다 더 나은 점은 무엇일까요?

간단히 말해, `Option<T>`와 `T`(여기서 `T`는 어떤 타입이든 될 수 있음)는 서로 다른 타입이기 때문에, 컴파일러는 `Option<T>` 값을 반드시 유효한 값처럼 사용할 수 없게 막아줍니다. 예를 들어, 아래 코드는 `i8`과 `Option<i8>`을 더하려고 하므로 컴파일되지 않습니다:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/src/main.rs:here}}
```

이 코드를 실행하면 다음과 같은 오류 메시지가 나옵니다:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/output.txt}}
```

매우 강력한 오류입니다! 이 오류 메시지는 러스트가 `i8`과 `Option<i8>`을 더하는 방법을 알지 못한다는 뜻입니다. 두 타입이 다르기 때문입니다. 러스트에서 `i8` 타입의 값을 가지면, 컴파일러가 항상 유효한 값임을 보장합니다. 널을 확인하지 않고도 안심하고 사용할 수 있습니다. `Option<i8>`(혹은 다루는 값의 타입이 무엇이든)을 가지면, 값이 없을 수도 있으므로 반드시 그 경우를 처리해야 하며, 컴파일러가 그 처리를 강제합니다.

즉, `Option<T>`를 `T`로 변환해야만 `T` 타입의 연산을 할 수 있습니다. 일반적으로 널과 관련된 가장 흔한 문제, 즉 값이 널이 아니라고 잘못 가정하는 실수를 방지할 수 있습니다.

널이 아니라고 잘못 가정하는 위험을 없애면 코드에 대한 자신감이 높아집니다. 값이 널일 수도 있음을 표현하려면, 타입을 명시적으로 `Option<T>`로 지정해야 합니다. 그리고 그 값을 사용할 때는 널일 경우를 반드시 명시적으로 처리해야 합니다. 타입이 `Option<T>`가 아닌 곳에서는 값이 널이 아님을 _안전하게_ 가정할 수 있습니다. 널의 만연함을 제한하고 러스트 코드의 안전성을 높이기 위해 의도적으로 설계된 부분입니다.

그렇다면 `Option<T>` 타입의 값에서 `Some` 변이 안의 `T` 값을 꺼내서 사용하려면 어떻게 해야 할까요? `Option<T>` 열거형에는 다양한 상황에서 유용한 많은 메서드가 있습니다. [공식 문서][docs]<!-- ignore -->에서 확인해보세요. `Option<T>`의 메서드에 익숙해지면 러스트를 배우는 데 큰 도움이 됩니다.

일반적으로 `Option<T>` 값을 사용할 때는 각 변이에 맞는 코드를 작성해야 합니다. `Some(T)` 값이 있을 때만 실행되는 코드에서는 내부의 `T` 값을 사용할 수 있습니다. `None` 값일 때만 실행되는 코드에서는 `T` 값이 없습니다. 열거형과 함께 사용하는 `match` 표현식은 바로 이런 경우에 각 변이에 따라 다른 코드를 실행하며, 해당 값 안의 데이터를 사용할 수 있게 해줍니다.

[IpAddr]: ../std/net/enum.IpAddr.html
[option]: ../std/option/enum.Option.html
[docs]: ../std/option/enum.Option.html