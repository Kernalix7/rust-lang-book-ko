<!-- 이전 제목. 삭제하지 마세요. 링크가 깨질 수 있습니다. -->

<a id="the-match-control-flow-operator"></a>

## `match` 제어 흐름 구조

러스트에는 매우 강력한 제어 흐름 구조인 `match`가 있습니다. `match`는 값을 여러 패턴과 비교한 뒤, 어떤 패턴과 일치하는지에 따라 코드를 실행할 수 있게 해줍니다. 패턴은 리터럴 값, 변수 이름, 와일드카드 등 다양한 형태로 만들 수 있습니다. [19장][ch19-00-patterns]<!-- ignore -->에서는 패턴의 종류와 동작을 더 자세히 다룹니다. `match`의 강점은 패턴의 표현력과, 컴파일러가 모든 가능한 경우를 반드시 처리하도록 확인해 준다는 점에 있습니다.

`match` 표현식은 동전 분류기를 떠올리면 이해하기 쉽습니다: 동전이 여러 크기의 구멍이 있는 트랙을 따라 내려가다가, 처음으로 맞는 구멍을 만나면 그 구멍으로 떨어집니다. 마찬가지로, 값이 각 패턴을 차례로 비교하다가 처음으로 "맞는" 패턴을 만나면, 그 패턴에 연결된 코드 블록이 실행됩니다.

동전을 예로 들어, `match`를 활용해 보겠습니다! 미국 동전을 받아서, 동전의 종류에 따라 센트 값을 반환하는 함수를 아래 Listing 6-3처럼 작성할 수 있습니다.

<Listing number="6-3" caption="열거형의 변형을 패턴으로 사용하는 `enum`과 `match` 표현식">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-03/src/main.rs:here}}
```

</Listing>

`value_in_cents` 함수의 `match`를 자세히 살펴봅시다. 먼저 `match` 키워드와 그 뒤에 비교할 표현식(여기서는 `coin` 값)을 작성합니다. 이는 `if`와 비슷해 보이지만, 중요한 차이가 있습니다: `if`의 조건은 반드시 불리언이어야 하지만, 여기서는 어떤 타입이든 가능합니다. 이 예시에서 `coin`의 타입은 우리가 첫 줄에 정의한 `Coin` 열거형입니다.

다음은 `match`의 팔(arm)입니다. 팔은 패턴과 코드로 이루어집니다. 첫 번째 팔의 패턴은 `Coin::Penny`이고, `=>` 연산자로 패턴과 실행할 코드를 구분합니다. 여기서는 단순히 값 `1`을 반환합니다. 각 팔은 쉼표로 구분합니다.

`match` 표현식이 실행되면, 값과 각 팔의 패턴을 차례로 비교합니다. 패턴이 값과 일치하면, 해당 패턴에 연결된 코드가 실행됩니다. 일치하지 않으면 다음 팔로 넘어갑니다. 동전 분류기와 마찬가지로, 첫 번째로 맞는 패턴에서 실행이 멈춥니다.

팔에 연결된 코드는 표현식이며, 일치한 팔의 표현식 결과가 전체 `match` 표현식의 반환값이 됩니다.

팔의 코드가 짧을 때는 중괄호를 생략하는 것이 일반적입니다. Listing 6-3처럼 각 팔이 값을 반환하는 경우가 그렇습니다. 여러 줄의 코드를 실행하려면 중괄호를 사용해야 하며, 이때는 팔 뒤의 쉼표가 선택적입니다. 예를 들어, 아래 코드는 `Coin::Penny`가 들어오면 "Lucky penny!"를 출력하고, 마지막 값인 `1`을 반환합니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-08-match-arm-multiple-lines/src/main.rs:here}}
```

### 값에 바인딩하는 패턴

`match` 팔의 또 다른 유용한 기능은, 패턴이 일치한 값의 일부를 변수에 바인딩할 수 있다는 점입니다. 이를 통해 열거형 변형 안의 값을 꺼낼 수 있습니다.

예를 들어, 열거형 변형 중 하나에 데이터를 넣어봅시다. 1999년부터 2008년까지 미국에서는 50개 주마다 다른 디자인의 쿼터 동전을 발행했습니다. 다른 동전에는 주 디자인이 없으므로, 쿼터만 추가 정보를 가집니다. 아래 Listing 6-4처럼, `Quarter` 변형에 `UsState` 값을 넣어 정보를 추가할 수 있습니다.

<Listing number="6-4" caption="`Quarter` 변형에 `UsState` 값을 포함하는 `Coin` 열거형">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-04/src/main.rs:here}}
```

</Listing>

친구가 50개 주의 쿼터를 모두 모으고 싶어한다고 가정해 봅시다. 동전을 종류별로 분류하면서, 쿼터가 나오면 해당 주 이름을 불러주어 친구가 없는 주라면 수집할 수 있게 해줍니다.

이 코드의 `match` 표현식에서는, `Coin::Quarter` 변형에 일치하는 패턴에 `state`라는 변수를 추가했습니다. 만약 값이 `Coin::Quarter`라면, `state` 변수에 해당 쿼터의 주 정보가 바인딩됩니다. 이제 `state`를 팔의 코드에서 사용할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-09-variable-in-pattern/src/main.rs:here}}
```

예를 들어, `value_in_cents(Coin::Quarter(UsState::Alaska))`를 호출하면, `coin`은 `Coin::Quarter(UsState::Alaska)`가 됩니다. 각 팔의 패턴과 비교하다가, `Coin::Quarter(state)`에서 일치합니다. 이때 `state`에는 `UsState::Alaska`가 바인딩됩니다. 이제 `println!`에서 `state` 값을 사용할 수 있으므로, 쿼터 변형 안의 주 정보를 꺼낼 수 있습니다.

### `Option<T>`와 매칭하기

앞 절에서는 `Option<T>`의 `Some` 변형 안의 값을 꺼내고 싶었습니다. `Option<T>`도 `match`로 처리할 수 있습니다. 동전을 비교하는 대신, `Option<T>`의 변형을 비교하지만, `match`의 동작 방식은 동일합니다.

예를 들어, `Option<i32>`를 받아 값이 있으면 1을 더하고, 값이 없으면 그대로 `None`을 반환하는 함수를 작성한다고 가정해 봅시다.

이 함수는 `match` 덕분에 매우 쉽게 작성할 수 있으며, 아래 Listing 6-5와 같습니다.

<Listing number="6-5" caption="`Option<i32>`에 대해 `match` 표현식을 사용하는 함수">

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:here}}
```

</Listing>

첫 번째로 `plus_one(five)`를 실행하면, 함수 내부의 `x`는 `Some(5)` 값을 가집니다. 이제 각 팔과 비교합니다:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

`Some(5)`는 `None` 패턴과 일치하지 않으므로, 다음 팔로 넘어갑니다:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:second_arm}}
```

`Some(5)`는 `Some(i)` 패턴과 일치합니다! 같은 변형이므로, `i`에 `Some` 안의 값인 `5`가 바인딩됩니다. 이제 팔의 코드가 실행되어, `i`에 1을 더한 뒤, 새로운 `Some` 값에 결과(`6`)를 넣어 반환합니다.

Listing 6-5의 두 번째 호출에서는, `x`가 `None`입니다. `match`에 들어가서 첫 번째 팔과 비교합니다:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

이번에는 일치합니다! 더할 값이 없으므로, 오른쪽의 `None` 값을 반환하고, 프로그램은 멈춥니다. 첫 번째 팔이 일치했으므로, 다른 팔은 비교하지 않습니다.

열거형과 `match`를 결합하면 다양한 상황을 쉽게 처리할 수 있습니다. 러스트 코드에서는 이런 패턴을 자주 보게 됩니다: 열거형을 `match`로 비교하고, 내부 데이터를 변수에 바인딩한 뒤, 그 값에 따라 코드를 실행합니다. 처음에는 다소 복잡하게 느껴질 수 있지만, 익숙해지면 모든 언어에 있었으면 좋겠다고 느낄 만큼 강력한 기능입니다. 러스트 사용자들이 특히 좋아하는 기능이기도 합니다.

### `match`는 반드시 모든 경우를 처리해야 한다

`match`의 또 다른 중요한 특징은, 팔의 패턴이 모든 가능한 경우를 반드시 처리해야 한다는 점입니다. 예를 들어, 아래처럼 버그가 있는 `plus_one` 함수는 컴파일되지 않습니다:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/src/main.rs:here}}
```

`None` 경우를 처리하지 않았으므로, 이 코드는 버그를 유발합니다. 다행히도, 러스트는 이런 버그를 잡아낼 수 있습니다. 컴파일하면 다음과 같은 에러가 발생합니다:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/output.txt}}
```

러스트는 모든 가능한 경우를 처리하지 않았음을 알려주며, 어떤 패턴을 빠뜨렸는지도 알려줍니다! 러스트의 `match`는 _완전성(exhaustiveness)_을 요구합니다: 모든 경우를 반드시 처리해야만 코드가 유효합니다. 특히 `Option<T>`의 경우, 러스트가 `None`을 반드시 처리하도록 강제하므로, 값이 있다고 잘못 가정하는 실수를 방지할 수 있습니다. 앞서 언급한 10억 달러짜리 실수를 러스트에서는 원천적으로 막을 수 있습니다.

### 포괄 패턴과 `_` 플레이스홀더

열거형을 사용할 때, 특정 값에만 특별한 동작을 하고, 나머지 값에는 기본 동작을 하고 싶을 때가 있습니다. 예를 들어, 주사위를 굴려 3이 나오면 플레이어가 움직이지 않고 멋진 모자를 얻고, 7이 나오면 모자를 잃고, 그 외에는 나온 숫자만큼 이동한다고 가정해 봅시다. 아래 코드는 이런 로직을 `match`로 구현합니다. 주사위 결과는 하드코딩되어 있고, 실제 동작은 함수 이름만 있고 본문은 생략했습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-15-binding-catchall/src/main.rs:here}}
```

첫 두 팔의 패턴은 리터럴 값 `3`과 `7`입니다. 마지막 팔은 모든 다른 값을 처리하며, 패턴에 변수 이름 `other`를 사용합니다. 이 팔의 코드는 `other` 값을 `move_player` 함수에 넘깁니다.

이 코드는 모든 가능한 `u8` 값을 나열하지 않아도 컴파일됩니다. 마지막 팔의 패턴이 나머지 모든 값을 처리하기 때문입니다. 이 포괄 패턴 덕분에 `match`의 완전성 요구를 만족합니다. 포괄 팔은 반드시 마지막에 와야 합니다. 패턴은 순서대로 평가되므로, 포괄 팔이 먼저 나오면 다른 팔이 실행되지 않으므로, 러스트가 경고를 표시합니다.

포괄 패턴에서 값을 사용하지 않을 때는 `_`라는 특별한 패턴을 사용할 수 있습니다. `_`는 어떤 값이든 일치하지만, 그 값을 바인딩하지 않습니다. 즉, 값을 사용하지 않을 것임을 러스트에 명확히 알릴 수 있습니다.

게임 규칙을 바꿔서, 3이나 7이 아닌 값이 나오면 다시 굴려야 한다고 가정해 봅시다. 이제 포괄 패턴의 값을 사용할 필요가 없으므로, 변수 대신 `_`를 사용할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-16-underscore-catchall/src/main.rs:here}}
```

이 예시도 완전성 요구를 만족합니다. 마지막 팔에서 나머지 모든 값을 명시적으로 무시하므로, 빠뜨린 경우가 없습니다.

마지막으로, 게임 규칙을 또 바꿔서 3이나 7이 아닌 값이 나오면 아무 일도 일어나지 않게 한다고 가정해 봅시다. 이때는 `_` 팔에 유닛 값(빈 튜플 타입)을 코드로 넣으면 됩니다:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-17-underscore-unit/src/main.rs:here}}
```

이 코드는, 앞선 팔에 일치하지 않는 값이 들어오면 아무 코드도 실행하지 않겠다는 뜻을 러스트에 명확히 알립니다.

패턴과 매칭에 대해서는 [19장][ch19-00-patterns]<!-- ignore -->에서 더 자세히 다룹니다. 지금은 `match` 표현식이 너무 장황할 때 유용한 `if let` 문법을 배워봅시다.

[tuples]: ch03-02-data-types.html#the-tuple-type
[ch19-00-patterns]: ch19-00-patterns.html