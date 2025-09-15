## 제어 흐름

조건이 `true`일 때만 코드를 실행하거나, 조건이 `true`인 동안 반복적으로 코드를 실행하는 기능은 대부분의 프로그래밍 언어에서 기본적인 빌딩 블록입니다. 러스트에서 코드의 실행 흐름을 제어하는 가장 흔한 구조는 `if` 표현식과 루프입니다.

### `if` 표현식

`if` 표현식은 조건에 따라 코드를 분기할 수 있게 해줍니다. 조건을 제공하고, "이 조건이 만족되면 이 코드 블록을 실행하고, 만족되지 않으면 실행하지 않는다"고 지정할 수 있습니다.

`if` 표현식을 실습해 보기 위해 _branches_라는 새 프로젝트를 만들고, _src/main.rs_ 파일에 아래 코드를 입력하세요:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/src/main.rs}}
```

모든 `if` 표현식은 `if` 키워드로 시작하며, 그 뒤에 조건이 옵니다. 여기서는 `number` 변수의 값이 5보다 작은지 검사합니다. 조건이 `true`면, 중괄호 안의 코드 블록이 실행됩니다. `if` 표현식의 조건에 연결된 코드 블록을 _팔(arm)_이라고 부르기도 합니다. 이는 [2장 "추측값과 비밀 숫자 비교하기"][comparing-the-guess-to-the-secret-number]<!-- ignore -->에서 다룬 `match` 표현식의 팔과 유사합니다.

선택적으로 `else` 표현식을 추가할 수 있습니다. 여기서는 조건이 `false`일 때 실행할 대체 코드 블록을 제공합니다. `else` 표현식을 제공하지 않으면, 조건이 `false`일 때 `if` 블록을 건너뛰고 다음 코드를 실행합니다.

이 코드를 실행하면 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/output.txt}}
```

`number` 값을 조건이 `false`가 되도록 바꿔서 실행해 보면 다음과 같습니다:

```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/src/main.rs:here}}
```

출력 결과:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/output.txt}}
```

조건은 반드시 `bool` 타입이어야 합니다. 만약 조건이 `bool`이 아니면 에러가 발생합니다. 예를 들어, 아래 코드를 실행해 보세요:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/src/main.rs}}
```

이번에는 조건이 `3`이므로, 러스트는 에러를 발생시킵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/output.txt}}
```

러스트는 `bool`을 기대했지만 정수를 받았다고 알려줍니다. Ruby나 JavaScript 같은 언어와 달리, 러스트는 자동으로 비불리언 타입을 불리언으로 변환하지 않습니다. 항상 명시적으로 `bool`을 제공해야 합니다. 예를 들어, 숫자가 0이 아닐 때만 실행하고 싶다면 아래처럼 작성해야 합니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-29-if-not-equal-0/src/main.rs}}
```

이 코드를 실행하면 `number was something other than zero`가 출력됩니다.

#### 여러 조건 처리: `else if`

여러 조건을 처리하려면 `if`와 `else`를 조합한 `else if` 표현식을 사용할 수 있습니다. 예를 들어:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/src/main.rs}}
```

이 프로그램은 네 가지 경로 중 하나를 선택합니다. 실행하면 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/output.txt}}
```

프로그램은 각 `if` 표현식을 차례로 검사하며, 첫 번째로 조건이 `true`인 팔의 코드만 실행합니다. 예를 들어, 6은 2로도 나눠지지만, 첫 번째로 `true`가 된 팔만 실행하고 나머지는 검사하지 않습니다.

`else if`를 너무 많이 사용하면 코드가 복잡해질 수 있으니, 여러 조건이 있다면 코드를 리팩터링하는 것이 좋습니다. 6장에서는 이런 경우에 강력한 분기 구조인 `match`를 소개합니다.

#### `let` 문에서 `if` 사용하기

`if`는 표현식이므로, `let` 문 오른쪽에 사용하여 결과를 변수에 할당할 수 있습니다. 리스트 3-2를 참고하세요.

<Listing number="3-2" file-name="src/main.rs" caption="`if` 표현식 결과를 변수에 할당하기">

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-02/src/main.rs}}
```

</Listing>

`number` 변수는 `if` 표현식의 결과에 따라 값이 결정됩니다. 코드를 실행하면 다음과 같은 결과가 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-02/output.txt}}
```

코드 블록은 마지막 표현식의 값을 평가하며, 숫자 자체도 표현식입니다. 즉, 어떤 팔이 실행되느냐에 따라 전체 `if` 표현식의 값이 결정됩니다. 각 팔에서 반환될 수 있는 값의 타입은 반드시 같아야 합니다. 만약 타입이 다르면 아래와 같은 에러가 발생합니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/src/main.rs}}
```

컴파일하면 에러가 발생합니다. `if`와 `else` 팔의 값 타입이 다르기 때문입니다. 러스트는 문제 위치를 정확히 알려줍니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/output.txt}}
```

`if` 블록은 정수, `else` 블록은 문자열을 반환합니다. 변수는 하나의 타입만 가져야 하며, 러스트는 컴파일 타임에 타입을 확정해야 합니다. 타입이 확정되어야 변수 사용 위치마다 타입 검증이 가능합니다. 만약 타입이 런타임에만 결정된다면, 컴파일러가 모든 경우의 타입을 추적해야 하므로 복잡해지고, 코드에 대한 보장도 줄어듭니다.

### 루프로 반복하기

코드를 여러 번 실행해야 할 때가 많습니다. 러스트는 여러 종류의 _루프_를 제공합니다. 루프는 코드 블록을 끝까지 실행한 뒤, 바로 처음으로 돌아가 다시 실행합니다. 루프를 실습해 보기 위해 _loops_라는 새 프로젝트를 만들어 봅시다.

러스트에는 `loop`, `while`, `for` 세 가지 루프가 있습니다. 각각을 살펴봅시다.

#### `loop`로 반복 코드 실행하기

`loop` 키워드는 코드 블록을 무한히 반복 실행합니다. 아래는 예시입니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-loop/src/main.rs}}
```

이 프로그램을 실행하면, `again!`이 계속 반복해서 출력됩니다. 프로그램을 수동으로 중단하려면 대부분의 터미널에서 <kbd>ctrl</kbd>-<kbd>c</kbd>를 사용할 수 있습니다. 실제로 실행해 보세요:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-32-loop
cargo run
CTRL-C
-->

```console
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.08s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

`^C`는 <kbd>ctrl</kbd>-<kbd>c</kbd>를 누른 위치를 나타냅니다.

코드가 루프 중단 신호를 받을 때 어디에 있었는지에 따라, `^C` 뒤에 `again!`이 출력될 수도 있고 아닐 수도 있습니다.

다행히도, 러스트는 코드로 루프를 종료하는 방법도 제공합니다. 루프 안에 `break` 키워드를 넣으면, 프로그램이 루프 실행을 중단합니다. 2장 [“정답을 맞추면 종료하기”][quitting-after-a-correct-guess]<!-- ignore -->에서 사용했던 방식입니다.

또한, `continue`를 사용하면 루프의 현재 반복에서 남은 코드를 건너뛰고 다음 반복으로 넘어갑니다.

#### 루프에서 값 반환하기

`loop`는 실패할 수 있는 작업을 재시도하거나, 스레드가 작업을 끝냈는지 확인할 때 자주 사용합니다. 루프에서 작업 결과를 루프 밖으로 전달해야 할 때는, `break` 뒤에 반환할 값을 지정하면 됩니다. 그 값이 루프에서 반환되어 사용할 수 있습니다:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-33-return-value-from-loop/src/main.rs}}
```

루프 전에 `counter` 변수를 0으로 초기화합니다. 그리고 `result` 변수에 루프에서 반환된 값을 저장합니다. 루프가 반복될 때마다 `counter`에 1을 더하고, `counter`가 10이 되면 `break`와 함께 `counter * 2` 값을 반환합니다. 루프 이후에는 세미콜론으로 문장을 끝내고, `result` 값을 출력합니다. 결과는 20입니다.

루프 안에서 `return`을 사용할 수도 있습니다. `break`는 현재 루프만 종료하지만, `return`은 항상 현재 함수 전체를 종료합니다.

#### 루프 라벨로 여러 루프 구분하기

루프가 중첩되어 있을 때, `break`와 `continue`는 가장 안쪽 루프에 적용됩니다. 루프에 _라벨_을 붙이면, 해당 라벨을 `break`나 `continue`와 함께 사용하여 바깥쪽 루프를 종료할 수 있습니다. 라벨은 작은따옴표로 시작합니다. 아래는 두 개의 중첩 루프 예시입니다:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/src/main.rs}}
```

바깥쪽 루프는 `'counting_up` 라벨이 붙어 있으며, 0부터 2까지 카운트합니다. 안쪽 루프는 10부터 9까지 카운트합니다. 라벨 없는 첫 번째 `break`는 안쪽 루프만 종료합니다. `break 'counting_up;`는 바깥쪽 루프를 종료합니다. 출력 결과는 다음과 같습니다:

```console
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/output.txt}}
```

#### 조건부 루프: `while`

프로그램에서 조건이 `true`인 동안 루프를 실행해야 할 때가 많습니다. 조건이 `true`인 동안 루프가 실행되고, 조건이 `false`가 되면 `break`로 루프가 종료됩니다. 이런 동작은 `loop`, `if`, `else`, `break` 조합으로도 구현할 수 있지만, 워낙 흔한 패턴이므로 러스트에는 `while` 루프가 내장되어 있습니다. 리스트 3-3은 `while`을 사용해 프로그램을 세 번 반복하고, 카운트다운 후 메시지를 출력합니다.

<Listing number="3-3" file-name="src/main.rs" caption="조건이 `true`인 동안 코드를 실행하는 `while` 루프 사용하기">

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-03/src/main.rs}}
```

</Listing>

이 구조는 `loop`, `if`, `else`, `break`를 사용할 때 필요한 중첩을 줄여주며, 더 명확합니다. 조건이 `true`인 동안 코드를 실행하고, 그렇지 않으면 루프를 종료합니다.

#### 컬렉션을 반복하는 `for` 루프

컬렉션(예: 배열)의 각 요소를 반복하려면 `while`을 사용할 수도 있습니다. 예를 들어, 리스트 3-4의 루프는 배열 `a`의 각 요소를 출력합니다.

<Listing number="3-4" file-name="src/main.rs" caption="`while` 루프로 컬렉션의 각 요소 반복하기">

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-04/src/main.rs}}
```

</Listing>

이 코드는 배열의 각 요소를 인덱스로 접근합니다. 인덱스는 0부터 시작하며, 배열의 마지막 인덱스에 도달할 때까지 반복합니다. 실행하면 배열의 모든 요소가 출력됩니다:

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-04/output.txt}}
```

배열의 모든 값이 터미널에 출력됩니다. 인덱스가 5에 도달하면, 루프는 여섯 번째 값을 가져오려 하지 않고 종료합니다.

하지만 이 방식은 오류가 발생하기 쉽습니다. 인덱스 값이나 조건이 잘못되면 프로그램이 패닉할 수 있습니다. 예를 들어, 배열 `a`를 네 개 요소로 바꿨는데 조건을 `while index < 4`로 수정하지 않으면, 코드가 패닉합니다. 또한, 매 반복마다 인덱스가 배열 범위 내에 있는지 검사하는 런타임 코드가 추가되어 느려질 수 있습니다.

더 간결한 대안으로, `for` 루프를 사용하여 컬렉션의 각 항목에 대해 코드를 실행할 수 있습니다. `for` 루프는 리스트 3-5처럼 작성합니다.

<Listing number="3-5" file-name="src/main.rs" caption="`for` 루프로 컬렉션의 각 요소 반복하기">

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-05/src/main.rs}}
```

</Listing>

이 코드를 실행하면 리스트 3-4와 동일한 출력이 나옵니다. 더 중요한 점은, 코드의 안전성이 높아지고, 배열 끝을 넘어가거나 일부 항목을 놓치는 버그가 사라집니다. `for` 루프에서 생성되는 머신 코드는 인덱스를 매 반복마다 배열 길이와 비교하지 않아도 되므로 더 효율적일 수 있습니다.

`for` 루프를 사용하면 배열의 값 개수를 바꿔도 다른 코드를 수정할 필요가 없습니다.

`for` 루프의 안전성과 간결함 덕분에, 러스트에서 가장 흔히 사용하는 루프 구조입니다. 반복 횟수가 정해진 경우에도, 대부분의 러스타시안은 `for` 루프를 사용합니다. 그럴 때는 표준 라이브러리의 `Range`를 사용하여, 시작 숫자부터 끝 숫자 전까지의 모든 숫자를 생성할 수 있습니다.

아래는 카운트다운을 `for` 루프와 `rev` 메서드(아직 설명하지 않은 메서드)로 뒤집어서 구현한 예시입니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-34-for-range/src/main.rs}}
```

이 코드는 훨씬 깔끔합니다.

## 요약

축하합니다! 이번 장은 상당히 많은 내용을 다뤘습니다: 변수, 스칼라와 복합 데이터 타입, 함수, 주석, `if` 표현식, 그리고 루프까지 배웠습니다! 이번 장에서 배운 개념을 연습하려면 다음과 같은 프로그램을 만들어 보세요:

- 화씨와 섭씨 온도 변환 프로그램
- n번째 피보나치 수 생성 프로그램
- 크리스마스 캐롤 "The Twelve Days of Christmas"의 가사를 반복 구조를 활용해 출력하는 프로그램

준비가 되었다면, 이제 러스트에서 흔히 볼 수 없는 개념인 _소유권_을 다루는 다음 장으로 넘어가겠습니다.

[comparing-the-guess-to-the-secret-number]: ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[quitting-after-a-correct-guess]: ch02-00-guessing-game-tutorial.html#quitting-after-a-correct-guess