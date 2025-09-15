## 함수

함수는 러스트 코드에서 매우 흔하게 사용됩니다. 이미 언어에서 가장 중요한 함수 중 하나인 `main` 함수를 보셨을 것입니다. `main` 함수는 많은 프로그램의 진입점입니다. 또한 새로운 함수를 선언할 수 있게 해주는 `fn` 키워드도 보셨습니다.

러스트 코드에서는 함수와 변수 이름에 _스네이크 케이스_를 사용하는 것이 관례입니다. 모든 글자를 소문자로 쓰고, 단어 사이를 밑줄로 구분합니다. 아래는 함수 정의 예시가 포함된 프로그램입니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-16-functions/src/main.rs}}
```

러스트에서 함수를 정의하려면 `fn` 뒤에 함수 이름과 괄호를 입력합니다. 중괄호는 함수 본문의 시작과 끝을 컴파일러에게 알려줍니다.

정의한 함수는 이름과 괄호를 입력하여 호출할 수 있습니다. `another_function`이 프로그램에 정의되어 있으므로, `main` 함수 안에서 호출할 수 있습니다. 참고로, 소스 코드에서 `main` 함수 _뒤에_ `another_function`을 정의했지만, 앞에 정의해도 됩니다. 러스트는 함수가 어디에 정의되어 있는지 신경 쓰지 않고, 호출자가 볼 수 있는 스코프에만 있으면 됩니다.

함수를 더 깊이 탐구하기 위해 _functions_라는 새 바이너리 프로젝트를 시작해 봅시다. 위의 `another_function` 예제를 _src/main.rs_에 넣고 실행하면, 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-16-functions/output.txt}}
```

각 줄은 `main` 함수에 나타난 순서대로 실행됩니다. 먼저 "Hello, world!" 메시지가 출력되고, 그 다음 `another_function`이 호출되어 해당 메시지가 출력됩니다.

### 매개변수

함수에는 _매개변수_를 정의할 수 있습니다. 매개변수는 함수 시그니처의 일부인 특별한 변수입니다. 함수에 매개변수가 있으면, 해당 매개변수에 구체적인 값을 전달할 수 있습니다. 엄밀히 말하면, 구체적인 값은 _인자_라고 부르지만, 일상적으로는 매개변수와 인자를 혼용해서 사용합니다.

이번에는 `another_function`에 매개변수를 추가해 봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/src/main.rs}}
```

이 프로그램을 실행하면 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/output.txt}}
```

`another_function` 선언에는 `x`라는 매개변수가 하나 있습니다. `x`의 타입은 `i32`로 지정되어 있습니다. `another_function`에 `5`를 전달하면, `println!` 매크로가 포맷 문자열의 중괄호에 `x` 값을 넣어줍니다.

함수 시그니처에서는 반드시 각 매개변수의 타입을 명시해야 합니다. 이는 러스트 설계에서 의도적으로 결정된 사항입니다. 함수 정의에서 타입을 명시하면, 컴파일러가 코드의 다른 부분에서 타입을 추론할 때 거의 타입 명시를 요구하지 않게 됩니다. 또한 컴파일러가 함수가 기대하는 타입을 알면 더 도움이 되는 오류 메시지를 제공할 수 있습니다.

매개변수가 여러 개일 때는, 매개변수 선언을 쉼표로 구분합니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/src/main.rs}}
```

이 예제는 `print_labeled_measurement`라는 함수에 두 개의 매개변수를 만듭니다. 첫 번째 매개변수는 `value`이고 타입은 `i32`입니다. 두 번째는 `unit_label`이고 타입은 `char`입니다. 함수는 두 값을 포함한 텍스트를 출력합니다.

이 코드를 실행해 봅시다. 현재 _functions_ 프로젝트의 _src/main.rs_ 파일에 위 예제를 넣고 `cargo run`으로 실행하세요:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/output.txt}}
```

함수를 호출할 때 `value`에 `5`, `unit_label`에 `'h'`를 전달했으므로, 프로그램 출력에 이 값들이 포함됩니다.

### 문장과 표현식

함수 본문은 일련의 문장으로 이루어져 있으며, 선택적으로 마지막에 표현식이 올 수 있습니다. 지금까지 다룬 함수들은 마지막에 표현식이 없었지만, 문장 일부로 표현식을 사용한 적은 있습니다. 러스트는 표현식 기반 언어이므로, 이 차이를 이해하는 것이 중요합니다. 다른 언어에서는 이런 구분이 없으니, 문장과 표현식이 무엇이고, 이 차이가 함수 본문에 어떤 영향을 주는지 살펴봅시다.

- 문장(statement)은 어떤 동작을 수행하지만 값을 반환하지 않습니다.
- 표현식(expression)은 결과값으로 평가됩니다.

예시를 살펴봅시다.

이미 문장과 표현식을 사용한 적이 있습니다. `let` 키워드로 변수를 만들고 값을 할당하는 것은 문장입니다. 리스트 3-1에서 `let y = 6;`은 문장입니다.

<Listing number="3-1" file-name="src/main.rs" caption="문장 하나가 포함된 `main` 함수 선언">

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-01/src/main.rs}}
```

</Listing>

함수 정의도 문장입니다. 위 예제 전체가 하나의 문장입니다. (아래에서 보겠지만, _함수 호출_은 문장이 아닙니다.)

문장은 값을 반환하지 않습니다. 따라서 아래 코드처럼 `let` 문장을 다른 변수에 할당할 수 없습니다. 오류가 발생합니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/src/main.rs}}
```

이 프로그램을 실행하면 다음과 같은 오류가 발생합니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/output.txt}}
```

`let y = 6` 문장은 값을 반환하지 않으므로, `x`에 바인딩할 값이 없습니다. 이는 C나 Ruby 같은 다른 언어와 다릅니다. 그런 언어에서는 할당이 할당된 값을 반환하므로, `x = y = 6`을 작성하면 `x`와 `y` 모두 값이 `6`이 됩니다. 러스트에서는 그렇지 않습니다.

표현식은 값을 평가하며, 러스트 코드의 대부분을 차지합니다. 예를 들어, `5 + 6`은 값을 `11`로 평가되는 표현식입니다. 표현식은 문장의 일부가 될 수 있습니다. 리스트 3-1의 `let y = 6;`에서 `6`은 값 `6`으로 평가되는 표현식입니다. 함수 호출도 표현식입니다. 매크로 호출도 표현식입니다. 중괄호로 만든 새 스코프 블록도 표현식입니다. 예를 들어:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-20-blocks-are-expressions/src/main.rs}}
```

이 표현식:

```rust,ignore
{
    let x = 3;
    x + 1
}
```

은 블록이며, 여기서는 값 `4`로 평가됩니다. 이 값은 `let` 문에서 `y`에 바인딩됩니다. 주목할 점은 `x + 1` 줄 끝에 세미콜론이 없다는 것입니다. 지금까지 본 대부분의 줄과 다릅니다. 표현식에는 끝에 세미콜론이 없습니다. 만약 표현식 끝에 세미콜론을 붙이면, 문장이 되어 값을 반환하지 않게 됩니다. 함수 반환값과 표현식을 탐구할 때 이 점을 기억하세요.

### 반환값이 있는 함수

함수는 호출한 코드에 값을 반환할 수 있습니다. 반환값에는 이름을 붙이지 않지만, 화살표(`->`) 뒤에 타입을 명시해야 합니다. 러스트에서는 함수의 반환값이 함수 본문 블록의 마지막 표현식 값과 동일합니다. `return` 키워드와 값을 사용해 일찍 반환할 수도 있지만, 대부분의 함수는 마지막 표현식을 암묵적으로 반환합니다. 아래는 값을 반환하는 함수 예시입니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/src/main.rs}}
```

`five` 함수에는 함수 호출, 매크로, `let` 문도 없습니다. 그냥 숫자 `5`만 있습니다. 러스트에서는 이런 함수도 완전히 유효합니다. 함수의 반환 타입도 `-> i32`로 명시되어 있습니다. 코드를 실행하면 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/output.txt}}
```

`five`의 `5`가 함수의 반환값이므로, 반환 타입이 `i32`입니다. 이 부분을 더 자세히 살펴봅시다. 중요한 점은 두 가지입니다. 첫째, `let x = five();` 줄은 함수의 반환값을 변수 초기화에 사용한다는 뜻입니다. `five` 함수가 `5`를 반환하므로, 이 줄은 다음과 같습니다:

```rust
let x = 5;
```

둘째, `five` 함수는 매개변수가 없고 반환값 타입을 정의하지만, 함수 본문에는 세미콜론 없이 `5`만 있습니다. 이는 반환하고 싶은 값인 표현식이기 때문입니다.

다른 예시도 살펴봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-22-function-parameter-and-return/src/main.rs}}
```

이 코드를 실행하면 `The value of x is: 6`이 출력됩니다. 하지만 `x + 1` 줄 끝에 세미콜론을 붙여 표현식을 문장으로 바꾸면 오류가 발생합니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/src/main.rs}}
```

이 코드를 컴파일하면 다음과 같은 오류가 발생합니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/output.txt}}
```

주요 오류 메시지인 `mismatched types`는 코드의 핵심 문제를 알려줍니다. `plus_one` 함수 정의는 `i32`를 반환한다고 되어 있지만, 문장은 값을 평가하지 않으므로 `()`(유닛 타입)으로 표현됩니다. 따라서 반환되는 값이 없어 함수 정의와 모순되어 오류가 발생합니다. 출력에서 러스트는 이 문제를 해결할 수 있도록 세미콜론을 제거하라고 안내합니다. 세미콜론을 없애면 오류가 해결됩니다.