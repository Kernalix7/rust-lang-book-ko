## 데이터 타입

러스트의 모든 값은 특정 _데이터 타입_을 가지고 있습니다. 데이터 타입은 러스트에게 어떤 종류의 데이터가 지정되었는지 알려주어, 해당 데이터를 어떻게 다뤄야 할지 결정하게 해줍니다. 이번 장에서는 데이터 타입의 두 가지 하위 집합인 스칼라와 복합 타입을 살펴봅니다.

러스트는 _정적 타입_ 언어임을 기억하세요. 즉, 컴파일 타임에 모든 변수의 타입을 알아야 합니다. 컴파일러는 값과 그 사용 방식에 따라 우리가 원하는 타입을 대부분 추론할 수 있습니다. 여러 타입이 가능한 경우, 예를 들어 [2장 "추측값과 비밀 숫자 비교하기"][comparing-the-guess-to-the-secret-number]<!-- ignore -->에서 `parse`로 `String`을 숫자 타입으로 변환할 때처럼, 타입 명시가 필요합니다:

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

위 코드에서 `: u32` 타입 명시를 하지 않으면, 러스트는 다음과 같은 에러를 표시합니다. 이는 컴파일러가 우리가 어떤 타입을 원하는지 더 많은 정보를 필요로 한다는 뜻입니다:

```console
{{#include ../listings/ch03-common-programming-concepts/output-only-01-no-type-annotations/output.txt}}
```

다른 데이터 타입에 대해서도 다양한 타입 명시를 보게 될 것입니다.

### 스칼라 타입

_스칼라_ 타입은 단일 값을 나타냅니다. 러스트에는 네 가지 주요 스칼라 타입이 있습니다: 정수, 부동소수점 숫자, 불리언, 문자. 다른 프로그래밍 언어에서도 익숙한 타입일 것입니다. 이제 러스트에서 이 타입들이 어떻게 동작하는지 살펴봅시다.

#### 정수 타입

_정수_란 소수점이 없는 숫자입니다. 2장에서 `u32` 타입을 사용한 적이 있습니다. 이 타입 선언은 해당 값이 부호 없는 정수(부호 있는 정수 타입은 `u` 대신 `i`로 시작함)이며, 32비트 공간을 차지함을 나타냅니다. 아래 표 3-1은 러스트에 내장된 정수 타입을 보여줍니다. 이 중 어떤 타입이든 정수 값의 타입으로 선언할 수 있습니다.

<span class="caption">표 3-1: 러스트의 정수 타입</span>

| 길이      | 부호 있음 | 부호 없음 |
| --------- | --------- | --------- |
| 8비트     | `i8`      | `u8`      |
| 16비트    | `i16`     | `u16`     |
| 32비트    | `i32`     | `u32`     |
| 64비트    | `i64`     | `u64`     |
| 128비트   | `i128`    | `u128`    |
| 아키텍처 의존 | `isize`   | `usize`   |

각 타입은 부호가 있거나 없으며, 명확한 크기를 가집니다. _부호 있음_과 _부호 없음_은 숫자가 음수일 수 있는지, 즉 숫자에 부호가 필요한지(부호 있음), 아니면 항상 양수만 표현하는지(부호 없음)를 의미합니다. 종이에 숫자를 쓸 때 부호가 중요하면 플러스나 마이너스 기호를 붙이고, 양수만 쓸 수 있다면 부호 없이 씁니다. 부호 있는 숫자는 [2의 보수][twos-complement]<!-- ignore --> 방식으로 저장됩니다.

각 부호 있는 타입은 −(2<sup>n − 1</sup>)부터 2<sup>n − 1</sup> − 1까지의 숫자를 저장할 수 있습니다. 여기서 _n_은 해당 타입이 사용하는 비트 수입니다. 예를 들어, `i8`은 −(2<sup>7</sup>)부터 2<sup>7</sup> − 1, 즉 −128부터 127까지 저장할 수 있습니다. 부호 없는 타입은 0부터 2<sup>n</sup> − 1까지 저장할 수 있으므로, `u8`은 0부터 255까지 저장할 수 있습니다.

또한, `isize`와 `usize` 타입은 프로그램이 실행되는 컴퓨터의 아키텍처에 따라 달라집니다: 64비트 아키텍처에서는 64비트, 32비트 아키텍처에서는 32비트입니다.

정수 리터럴은 표 3-2에 나온 다양한 형태로 작성할 수 있습니다. 여러 숫자 타입이 가능한 리터럴에는 타입 접미사(예: `57u8`)를 붙여 타입을 지정할 수 있습니다. 숫자 리터럴에는 `_`를 시각적 구분자로 사용할 수 있어, `1_000`은 `1000`과 동일한 값을 가집니다.

<span class="caption">표 3-2: 러스트의 정수 리터럴</span>

| 숫자 리터럴 | 예시           |
| ----------- | -------------- |
| 10진수      | `98_222`       |
| 16진수      | `0xff`         |
| 8진수       | `0o77`         |
| 2진수       | `0b1111_0000`  |
| 바이트(`u8`만) | `b'A'`      |

그렇다면 어떤 정수 타입을 사용해야 할까요? 확실하지 않다면, 러스트의 기본값을 사용하는 것이 좋습니다: 정수 타입은 기본적으로 `i32`입니다. `isize`나 `usize`는 컬렉션의 인덱싱 등에서 주로 사용합니다.

> ##### 정수 오버플로우
>
> 예를 들어, 0부터 255까지 값을 저장할 수 있는 `u8` 타입 변수가 있다고 합시다. 만약 이 변수에 256과 같이 범위를 벗어난 값을 할당하려 하면, _정수 오버플로우_가 발생합니다. 이때 두 가지 동작 중 하나가 일어날 수 있습니다. 디버그 모드로 컴파일할 때, 러스트는 정수 오버플로우를 검사하여 런타임에 오버플로우가 발생하면 프로그램이 _패닉_하도록 합니다. 러스트에서 _패닉_이란 프로그램이 에러와 함께 종료되는 것을 의미합니다. 패닉에 대해서는 [9장 "복구 불가능한 에러와 `panic!`"][unrecoverable-errors-with-panic]<!-- ignore -->에서 더 자세히 다룹니다.
>
> 반면, `--release` 플래그로 릴리스 모드로 컴파일하면, 러스트는 오버플로우 검사로 인한 패닉을 포함하지 않습니다. 대신 오버플로우가 발생하면 _2의 보수 래핑_을 수행합니다. 즉, 타입이 저장할 수 있는 최대값을 넘는 값은 타입이 저장할 수 있는 최소값으로 "돌아갑니다". 예를 들어, `u8`에서 256은 0이 되고, 257은 1이 됩니다. 프로그램은 패닉하지 않지만, 변수 값이 예상과 다를 수 있습니다. 오버플로우의 래핑 동작에 의존하는 것은 오류로 간주됩니다.
>
> 오버플로우 가능성을 명시적으로 처리하려면, 표준 라이브러리의 원시 숫자 타입에 제공되는 다음 메서드 계열을 사용할 수 있습니다:
>
> - 모든 모드에서 래핑 동작을 하려면 `wrapping_*` 메서드(예: `wrapping_add`)를 사용하세요.
> - 오버플로우 시 `None`을 반환하려면 `checked_*` 메서드를 사용하세요.
> - 값과 오버플로우 여부를 함께 반환하려면 `overflowing_*` 메서드를 사용하세요.
> - 값의 최소/최대값에서 포화 동작을 하려면 `saturating_*` 메서드를 사용하세요.

#### 부동소수점 타입

러스트에는 _부동소수점 숫자_를 위한 두 가지 원시 타입이 있습니다. 부동소수점 숫자는 소수점이 있는 숫자입니다. 러스트의 부동소수점 타입은 `f32`와 `f64`로, 각각 32비트와 64비트 크기를 가집니다. 기본 타입은 `f64`입니다. 현대 CPU에서는 `f32`와 거의 같은 속도로 동작하면서 더 높은 정밀도를 제공하기 때문입니다. 모든 부동소수점 타입은 부호가 있습니다.

아래는 부동소수점 숫자의 사용 예시입니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-06-floating-point/src/main.rs}}
```

부동소수점 숫자는 IEEE-754 표준에 따라 표현됩니다.

#### 숫자 연산

러스트는 모든 숫자 타입에 대해 기본적인 수학 연산을 지원합니다: 덧셈, 뺄셈, 곱셈, 나눗셈, 나머지. 정수 나눗셈은 0에 가까운 쪽으로 내림하여 정수로 만듭니다. 아래 코드는 각 숫자 연산을 `let` 문에서 사용하는 방법을 보여줍니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-07-numeric-operations/src/main.rs}}
```

각 문장의 표현식은 수학 연산자를 사용하며, 단일 값으로 평가되어 변수에 바인딩됩니다. [부록 B][appendix_b]<!-- ignore -->에는 러스트가 제공하는 모든 연산자 목록이 있습니다.

#### 불리언 타입

대부분의 프로그래밍 언어와 마찬가지로, 러스트의 불리언 타입은 두 가지 값만 가질 수 있습니다: `true`와 `false`. 불리언은 1바이트 크기입니다. 러스트에서 불리언 타입은 `bool`로 지정합니다. 예를 들면 다음과 같습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-08-boolean/src/main.rs}}
```

불리언 값을 사용하는 주요 방법은 조건문, 예를 들어 `if` 표현식입니다. 러스트에서 `if` 표현식이 어떻게 동작하는지는 [“제어 흐름”][control-flow]<!-- ignore -->에서 다룹니다.

#### 문자 타입

러스트의 `char` 타입은 언어에서 가장 원시적인 알파벳 타입입니다. 아래는 `char` 값을 선언하는 예시입니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-09-char/src/main.rs}}
```

`char` 리터럴은 작은따옴표로 지정하며, 문자열 리터럴은 큰따옴표를 사용합니다. 러스트의 `char` 타입은 4바이트 크기이며, 유니코드 스칼라 값을 나타냅니다. 즉, ASCII뿐만 아니라, 악센트가 있는 문자, 한중일 문자, 이모지, 제로폭 공백 등 다양한 문자를 표현할 수 있습니다. 유니코드 스칼라 값의 범위는 `U+0000`부터 `U+D7FF`, 그리고 `U+E000`부터 `U+10FFFF`까지입니다. 하지만 유니코드에서 "문자"라는 개념은 명확하지 않으므로, 여러분이 생각하는 "문자"와 러스트의 `char`가 일치하지 않을 수 있습니다. 이 주제는 [8장 "UTF-8 인코딩된 텍스트를 문자열로 저장하기"][strings]<!-- ignore -->에서 자세히 다룹니다.

### 복합 타입

_복합 타입_은 여러 값을 하나의 타입으로 묶을 수 있습니다. 러스트에는 두 가지 원시 복합 타입이 있습니다: 튜플과 배열입니다.

#### 튜플 타입

_튜플_은 다양한 타입의 여러 값을 하나의 복합 타입으로 묶는 일반적인 방법입니다. 튜플은 길이가 고정되어 있으며, 선언 후 크기를 변경할 수 없습니다.

튜플은 괄호 안에 쉼표로 구분된 값 목록을 작성하여 생성합니다. 튜플의 각 위치에는 타입이 있으며, 각 값의 타입은 서로 달라도 됩니다. 아래 예시에는 타입 명시가 선택적으로 추가되어 있습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-10-tuples/src/main.rs}}
```

`tup` 변수는 전체 튜플에 바인딩됩니다. 튜플은 하나의 복합 요소로 간주되기 때문입니다. 튜플에서 개별 값을 꺼내려면, 패턴 매칭을 사용해 튜플 값을 분해할 수 있습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-11-destructuring-tuples/src/main.rs}}
```

이 프로그램은 먼저 튜플을 생성하여 `tup`에 바인딩합니다. 그 다음, `let` 패턴을 사용해 `tup`을 세 개의 변수 `x`, `y`, `z`로 분해합니다. 이를 _분해(destructuring)_라고 하며, 하나의 튜플을 세 부분으로 나눕니다. 마지막으로 프로그램은 `y`의 값을 출력합니다. 값은 `6.4`입니다.

튜플의 요소를 직접 접근하려면, 마침표(`.`) 뒤에 접근하려는 값의 인덱스를 붙이면 됩니다. 예를 들어:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-12-tuple-indexing/src/main.rs}}
```

이 프로그램은 튜플 `x`를 생성한 뒤, 각 요소를 인덱스로 접근합니다. 대부분의 프로그래밍 언어와 마찬가지로, 튜플의 첫 번째 인덱스는 0입니다.

값이 없는 튜플은 특별한 이름인 _유닛(unit)_을 가집니다. 이 값과 타입은 모두 `()`로 표기하며, 빈 값이나 빈 반환 타입을 나타냅니다. 값이 없는 표현식은 암묵적으로 유닛 값을 반환합니다.

#### 배열 타입

여러 값을 모아 컬렉션을 만들려면 _배열_을 사용할 수 있습니다. 튜플과 달리, 배열의 모든 요소는 같은 타입이어야 합니다. 다른 언어의 배열과 달리, 러스트의 배열은 길이가 고정되어 있습니다.

배열의 값은 대괄호 안에 쉼표로 구분된 목록으로 작성합니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-13-arrays/src/main.rs}}
```

배열은 데이터를 스택에 할당하고 싶을 때, 또는 항상 고정된 개수의 요소가 필요할 때 유용합니다. (스택과 힙에 대해서는 [4장][stack-and-heap]<!-- ignore -->에서 더 자세히 다룹니다.) 배열은 벡터 타입만큼 유연하지는 않습니다. _벡터_는 표준 라이브러리에서 제공하는 컬렉션 타입으로, 힙에 저장되기 때문에 크기를 늘리거나 줄일 수 있습니다. 배열과 벡터 중 어느 것을 사용할지 확실하지 않다면, 대부분 벡터를 사용하는 것이 좋습니다. [8장][vectors]<!-- ignore -->에서 벡터를 더 자세히 다룹니다.

하지만 요소 개수가 변하지 않을 것이 확실하다면 배열이 더 유용합니다. 예를 들어, 프로그램에서 월 이름을 사용할 때는 항상 12개 요소가 있으므로 배열을 사용하는 것이 적합합니다:

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

배열의 타입은 대괄호 안에 각 요소의 타입, 세미콜론, 그리고 배열의 요소 개수를 작성하여 지정합니다:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

여기서 `i32`는 각 요소의 타입이고, 세미콜론 뒤의 `5`는 배열에 5개의 요소가 있음을 나타냅니다.

모든 요소가 같은 값으로 초기화된 배열을 만들려면, 초기값 뒤에 세미콜론과 배열 길이를 대괄호 안에 작성하면 됩니다:

```rust
let a = [3; 5];
```

이 배열 `a`는 5개의 요소를 가지며, 모두 처음에 값이 `3`으로 설정됩니다. 이는 `let a = [3, 3, 3, 3, 3];`와 동일하지만 더 간결한 방식입니다.

##### 배열 요소 접근하기

배열은 고정 크기의 메모리 덩어리로, 스택에 할당될 수 있습니다. 배열의 요소는 인덱싱을 통해 접근할 수 있습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-14-array-indexing/src/main.rs}}
```

이 예제에서 `first` 변수는 배열의 `[0]` 인덱스에 있는 값 `1`을, `second` 변수는 `[1]` 인덱스의 값 `2`를 갖게 됩니다.

##### 잘못된 배열 요소 접근

배열의 끝을 넘는 요소에 접근하면 어떻게 될까요? 2장의 숫자 맞추기 게임과 비슷하게, 사용자로부터 배열 인덱스를 입력받는 코드를 실행한다고 가정해 봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,panics
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/src/main.rs}}
```

이 코드는 컴파일은 성공합니다. `cargo run`으로 실행하고 `0`, `1`, `2`, `3`, `4`를 입력하면, 배열의 해당 인덱스 값을 출력합니다. 하지만 배열의 끝을 넘는 숫자(예: `10`)를 입력하면 다음과 같은 출력이 나옵니다:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access
cargo run
10
-->

```console
thread 'main' panicked at src/main.rs:19:19:
index out of bounds: the len is 5 but the index is 10
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

이 프로그램은 인덱싱 연산에서 잘못된 값을 사용한 시점에 _런타임_ 에러가 발생합니다. 프로그램은 에러 메시지와 함께 종료되며, 마지막 `println!` 문은 실행되지 않습니다. 인덱싱을 사용할 때, 러스트는 지정한 인덱스가 배열 길이보다 작은지 검사합니다. 만약 인덱스가 길이보다 크거나 같으면, 러스트는 패닉합니다. 이 검사는 런타임에 반드시 필요합니다. 특히 이 경우에는, 사용자가 코드를 실행할 때 어떤 값을 입력할지 컴파일러가 알 수 없기 때문입니다.

이것이 바로 러스트의 메모리 안전 원칙이 실제로 적용되는 예시입니다. 많은 저수준 언어에서는 이런 검사를 하지 않으며, 잘못된 인덱스를 제공하면 잘못된 메모리에 접근할 수 있습니다. 러스트는 이런 오류를 즉시 종료함으로써, 잘못된 메모리 접근을 방지합니다. 9장에서는 러스트의 에러 처리와, 패닉도 발생하지 않고 잘못된 메모리 접근도 허용하지 않는 안전하고 읽기 쉬운 코드를 작성하는 방법을 더 다룹니다.

[comparing-the-guess-to-the-secret-number]: ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[twos-complement]: https://en.wikipedia.org/wiki/Two%27s_complement
[control-flow]: ch03-05-control-flow.html#control-flow
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[stack-and-heap]: ch04-01-what-is-ownership.html#the-stack-and-the-heap
[vectors]: ch08-01-vectors.html
[unrecoverable-errors-with-panic]: ch09-01-unrecoverable-errors-with-panic.html
[appendix_b]: appendix-02-operators.md