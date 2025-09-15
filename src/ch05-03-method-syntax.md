## 메서드 문법

_메서드_는 함수와 비슷합니다. `fn` 키워드와 이름으로 선언하며, 매개변수와 반환값을 가질 수 있고, 호출될 때 실행되는 코드를 포함합니다. 하지만 함수와 달리, 메서드는 구조체(또는 열거형, 트레이트 객체—각각 [6장][enums]<!-- ignore -->, [18장][trait-objects]<!-- ignore -->에서 다룸) 맥락에서 정의되며, 첫 번째 매개변수는 항상 `self`입니다. `self`는 메서드가 호출되는 구조체 인스턴스를 나타냅니다.

### 메서드 정의하기

이제 `Rectangle` 인스턴스를 매개변수로 받던 `area` 함수를, `Rectangle` 구조체에 정의된 `area` 메서드로 바꿔봅시다. 리스트 5-13을 참고하세요.

<Listing number="5-13" file-name="src/main.rs" caption="`Rectangle` 구조체에 `area` 메서드 정의하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-13/src/main.rs}}
```

</Listing>

함수를 `Rectangle` 맥락에서 정의하려면, 먼저 `Rectangle`에 대한 `impl`(구현) 블록을 시작합니다. 이 블록 안의 모든 것은 `Rectangle` 타입과 연관됩니다. 그런 다음, `area` 함수를 `impl` 블록 안으로 옮기고, 첫 번째(그리고 여기서는 유일한) 매개변수를 시그니처와 본문에서 모두 `self`로 변경합니다. `main`에서 `area` 함수를 호출할 때 인자로 `rect1`을 넘기던 부분은, 이제 _메서드 문법_을 사용하여 `Rectangle` 인스턴스에서 직접 `area` 메서드를 호출할 수 있습니다. 메서드 문법은 인스턴스 뒤에 점(`.`), 메서드 이름, 괄호, 그리고 필요한 인자를 붙여 사용합니다.

`area` 시그니처에서는 `rectangle: &Rectangle` 대신 `&self`를 사용합니다. `&self`는 사실 `self: &Self`의 축약형입니다. `impl` 블록 안에서 `Self`는 해당 `impl` 블록의 타입을 의미합니다. 메서드는 첫 번째 매개변수로 타입이 `Self`인 `self`를 반드시 가져야 하므로, 러스트는 첫 번째 매개변수 자리에 `self`만 써도 되게 해줍니다. `self` 앞에 `&`를 붙여 불변 빌림임을 나타내는 것도 함수 버전과 동일합니다. 메서드는 `self`의 소유권을 가져오거나, 불변으로 빌리거나, 가변으로 빌릴 수 있습니다. 다른 매개변수와 마찬가지로 선택할 수 있습니다.

여기서는 함수 버전에서 `&Rectangle`을 사용했던 것과 같은 이유로 `&self`를 사용합니다. 소유권을 가져오지 않고, 구조체의 데이터를 읽기만 하고 싶기 때문입니다. 만약 메서드에서 인스턴스를 변경하고 싶다면, 첫 번째 매개변수를 `&mut self`로 지정해야 합니다. 인스턴스의 소유권을 가져오는 메서드는 드물게 사용되며, 주로 인스턴스를 다른 것으로 변환하고, 변환 후 원래 인스턴스를 더 이상 사용하지 못하게 하고 싶을 때 사용합니다.

메서드를 사용하는 주요 이유는, 메서드 문법을 제공하고, 모든 메서드 시그니처에 타입을 반복해서 쓸 필요가 없다는 점 외에도, 코드의 조직화에 있습니다. 타입 인스턴스에서 할 수 있는 모든 동작을 하나의 `impl` 블록에 모아두면, 라이브러리 사용자들이 여러 곳을 뒤지지 않고도 `Rectangle`의 기능을 쉽게 찾을 수 있습니다.

구조체의 필드와 같은 이름의 메서드를 정의할 수도 있습니다. 예를 들어, `Rectangle`에 `width`라는 메서드를 정의할 수 있습니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-06-method-field-interaction/src/main.rs:here}}
```

</Listing>

여기서는 인스턴스의 `width` 필드 값이 0보다 크면 `true`, 0이면 `false`를 반환하는 `width` 메서드를 정의했습니다. 메서드와 필드 이름이 같아도, `rect1.width()`처럼 괄호를 붙이면 메서드를, 괄호 없이 쓰면 필드를 의미합니다.

종종, 필드와 같은 이름의 메서드는 필드 값을 그대로 반환하는 _게터(getter)_로 사용합니다. 러스트는 다른 언어처럼 구조체 필드에 대해 자동으로 게터를 만들어주지 않습니다. 게터는 필드를 비공개로 두고, 메서드는 공개로 만들어 타입의 공개 API 일부로 읽기 전용 접근을 제공할 때 유용합니다. 공개/비공개 지정과 관련해서는 [7장][public]<!-- ignore -->에서 다룹니다.

> ### `->` 연산자는 어디에 있나요?
>
> C와 C++에서는 메서드 호출에 두 가지 연산자를 사용합니다. 객체에 직접 메서드를 호출할 때는 `.`을, 객체 포인터에서 메서드를 호출할 때는 `->`를 사용합니다. 즉, `object`가 포인터라면, `object->something()`은 `(*object).something()`과 비슷합니다.
>
> 러스트에는 `->` 연산자가 없습니다. 대신, 러스트에는 _자동 참조 및 역참조_ 기능이 있습니다. 메서드 호출은 러스트에서 이런 동작이 일어나는 몇 안 되는 곳 중 하나입니다.
>
> 동작 방식은 다음과 같습니다: `object.something()`으로 메서드를 호출하면, 러스트가 자동으로 `&`, `&mut`, 또는 `*`를 추가하여 `object`가 메서드 시그니처와 맞도록 만듭니다. 즉, 아래 두 코드는 동일하게 동작합니다:
>
> <!-- CAN'T EXTRACT SEE BUG https://github.com/rust-lang/mdBook/issues/1127 -->
>
> ```rust
> # #[derive(Debug,Copy,Clone)]
> # struct Point {
> #     x: f64,
> #     y: f64,
> # }
> #
> # impl Point {
> #    fn distance(&self, other: &Point) -> f64 {
> #        let x_squared = f64::powi(other.x - self.x, 2);
> #        let y_squared = f64::powi(other.y - self.y, 2);
> #
> #        f64::sqrt(x_squared + y_squared)
> #    }
> # }
> # let p1 = Point { x: 0.0, y: 0.0 };
> # let p2 = Point { x: 5.0, y: 6.5 };
> p1.distance(&p2);
> (&p1).distance(&p2);
> ```
>
> 첫 번째 방식이 훨씬 깔끔합니다. 자동 참조 기능은 메서드에 명확한 수신자—즉, `self` 타입—가 있기 때문에 동작합니다. 수신자와 메서드 이름이 주어지면, 러스트는 메서드가 읽기(`&self`), 변경(`&mut self`), 소유권 이전(`self`) 중 어떤 동작을 하는지 확실히 알 수 있습니다. 러스트가 메서드 수신자에 대해 빌림을 암묵적으로 처리해 주는 덕분에, 실제로 소유권이 편리하게 동작합니다.

### 매개변수가 더 많은 메서드

메서드 사용을 연습하기 위해, `Rectangle` 구조체에 두 번째 메서드를 구현해 봅시다. 이번에는 `Rectangle` 인스턴스가 다른 `Rectangle` 인스턴스를 받아, 두 번째 사각형이 첫 번째 사각형(`self`) 안에 완전히 들어갈 수 있으면 `true`, 아니면 `false`를 반환하도록 합니다. 즉, `can_hold` 메서드를 정의하면, 리스트 5-14처럼 프로그램을 작성할 수 있습니다.

<Listing number="5-14" file-name="src/main.rs" caption="아직 작성하지 않은 `can_hold` 메서드 사용 예시">

```rust,ignore
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-14/src/main.rs}}
```

</Listing>

예상 출력은 다음과 같습니다. `rect2`의 너비와 높이가 모두 `rect1`보다 작으므로 `rect1`이 `rect2`를 담을 수 있지만, `rect3`는 `rect1`보다 너비가 더 넓으므로 담을 수 없습니다.

```text
Can rect1 hold rect2? true
Can rect1 hold rect3? false
```

메서드를 정의해야 하므로, `impl Rectangle` 블록 안에 작성합니다. 메서드 이름은 `can_hold`이고, 다른 `Rectangle`의 불변 참조를 매개변수로 받습니다. 메서드를 호출하는 코드를 보면, `rect1.can_hold(&rect2)`처럼 `&rect2`를 넘깁니다. 즉, `rect2`의 불변 참조를 넘기는 것이며, 값을 변경하지 않고 읽기만 하므로 불변 참조가 적합합니다. 또한 `main`에서 `rect2`의 소유권을 계속 유지할 수 있습니다. 반환값은 불리언이며, `self`의 너비와 높이가 각각 다른 사각형의 너비와 높이보다 큰지 검사합니다. 이제 리스트 5-13의 `impl` 블록에 새 `can_hold` 메서드를 추가한 리스트 5-15를 참고하세요.

<Listing number="5-15" file-name="src/main.rs" caption="다른 `Rectangle` 인스턴스를 매개변수로 받는 `can_hold` 메서드 구현">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-15/src/main.rs:here}}
```

</Listing>

리스트 5-14의 `main` 함수와 함께 이 코드를 실행하면 원하는 출력이 나옵니다. 메서드는 `self` 매개변수 뒤에 여러 매개변수를 추가할 수 있으며, 함수의 매개변수와 동일하게 동작합니다.

### 연관 함수

`impl` 블록 안에 정의된 모든 함수는 _연관 함수_라고 부릅니다. 해당 함수가 `impl` 뒤에 오는 타입과 연관되어 있기 때문입니다. 연관 함수 중 첫 번째 매개변수가 `self`가 아니면, 인스턴스 없이도 사용할 수 있으므로 메서드가 아닙니다. 이미 이런 함수를 사용해 본 적이 있습니다: `String` 타입의 `String::from` 함수가 그 예입니다.

메서드가 아닌 연관 함수는 주로 구조체의 새 인스턴스를 반환하는 생성자 역할을 합니다. 이런 함수는 보통 `new`라고 부르지만, `new`는 특별한 이름이 아니며 언어에 내장된 것도 아닙니다. 예를 들어, 하나의 길이만 받아서 너비와 높이를 모두 같은 값으로 지정하는 `square`라는 연관 함수를 제공할 수도 있습니다. 이렇게 하면 같은 값을 두 번 지정하지 않고도 정사각형 `Rectangle`을 쉽게 만들 수 있습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-03-associated-functions/src/main.rs:here}}
```

반환 타입과 함수 본문의 `Self` 키워드는 `impl` 키워드 뒤에 오는 타입의 별칭입니다. 여기서는 `Rectangle`입니다.

이 연관 함수를 호출하려면, 구조체 이름과 함께 `::` 문법을 사용합니다. 예를 들어 `let sq = Rectangle::square(3);`처럼 작성합니다. 이 함수는 구조체의 네임스페이스에 속합니다. `::` 문법은 연관 함수와 모듈이 만든 네임스페이스 모두에 사용됩니다. 모듈에 대해서는 [7장][modules]<!-- ignore -->에서 다룹니다.

### 여러 개의 `impl` 블록

각 구조체는 여러 개의 `impl` 블록을 가질 수 있습니다. 예를 들어, 리스트 5-15의 코드는 각 메서드를 별도의 `impl` 블록에 넣은 리스트 5-16과 동일합니다.

<Listing number="5-16" caption="리스트 5-15를 여러 개의 `impl` 블록으로 다시 작성한 예시">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-16/src/main.rs:here}}
```

</Listing>

여기서는 메서드를 여러 `impl` 블록으로 나눌 이유가 없지만, 문법적으로는 가능합니다. 10장에서는 제네릭 타입과 트레이트를 다루면서 여러 `impl` 블록이 유용한 경우를 볼 수 있습니다.

## 요약

구조체를 사용하면 도메인에 의미 있는 사용자 정의 타입을 만들 수 있습니다. 구조체를 사용하면 관련된 데이터 조각을 서로 연결하고, 각 조각에 이름을 붙여 코드를 명확하게 만들 수 있습니다. `impl` 블록에서는 타입과 연관된 함수를 정의할 수 있고, 메서드는 구조체 인스턴스의 동작을 지정할 수 있는 연관 함수의 한 종류입니다.

하지만 구조체만이 사용자 정의 타입을 만드는 방법은 아닙니다. 이제 러스트의 열거형 기능을 살펴보며, 또 다른 도구를 배워봅시다.

[enums]: ch06-00-enums.html
[trait-objects]: ch18-02-trait-objects.md
[public]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#exposing-paths-with-the-pub-keyword
[modules]: ch07-02-defining-modules-to-control-scope-and-privacy.html