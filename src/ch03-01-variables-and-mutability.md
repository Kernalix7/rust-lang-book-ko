## 변수와 가변성

[“변수로 값 저장하기”][storing-values-with-variables]<!-- ignore -->에서 언급했듯이, 러스트에서 변수는 기본적으로 불변입니다. 이는 러스트가 제공하는 안전성과 쉬운 동시성의 이점을 최대한 활용할 수 있도록 여러분에게 권장하는 많은 습관 중 하나입니다. 하지만 변수의 가변성을 선택할 수도 있습니다. 러스트가 왜 불변성을 권장하는지, 그리고 때로는 왜 가변성을 선택할 수 있는지 살펴봅시다.

변수가 불변이면, 한 번 이름에 값을 바인딩하면 그 값을 변경할 수 없습니다. 이를 보여주기 위해 _projects_ 디렉터리에서 `cargo new variables`로 _variables_라는 새 프로젝트를 생성해 보세요.

새 _variables_ 디렉터리에서 _src/main.rs_ 파일을 열고 아래 코드를 입력하세요. 이 코드는 아직 컴파일되지 않습니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/src/main.rs}}
```

저장한 뒤 `cargo run`으로 프로그램을 실행하면, 불변성 오류에 관한 에러 메시지를 받을 것입니다. 출력 예시는 다음과 같습니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/output.txt}}
```

이 예제는 컴파일러가 프로그램의 오류를 찾아내는 데 어떻게 도움을 주는지 보여줍니다. 컴파일러 오류는 답답할 수 있지만, 사실 이는 프로그램이 아직 안전하게 원하는 동작을 하지 않는다는 뜻일 뿐입니다. 여러분이 좋은 프로그래머가 아니라는 의미는 _절대_ 아닙니다! 숙련된 러스타시안들도 컴파일러 오류를 자주 만납니다.

이 에러 메시지 `` cannot assign twice to immutable variable `x` ``는 불변 변수 `x`에 두 번째 값을 할당하려 했기 때문에 발생했습니다.

불변으로 지정된 값을 변경하려고 할 때 컴파일 타임에 오류가 발생하는 것은 매우 중요합니다. 이런 상황은 버그로 이어질 수 있기 때문입니다. 코드의 한 부분이 값이 절대 변하지 않을 것이라 가정하고, 다른 부분에서 그 값을 변경하면, 첫 번째 부분의 코드가 의도한 대로 동작하지 않을 수 있습니다. 특히 두 번째 코드가 _가끔만_ 값을 변경한다면, 이런 버그의 원인을 추적하기가 매우 어렵습니다. 러스트 컴파일러는 값이 변하지 않을 것이라고 명시하면, 실제로 변하지 않도록 보장해 주므로, 여러분이 직접 추적할 필요가 없습니다. 덕분에 코드를 더 쉽게 이해할 수 있습니다.

하지만 가변성은 매우 유용하며, 코드를 더 편리하게 작성할 수 있게 해줍니다. 변수는 기본적으로 불변이지만, [2장][storing-values-with-variables]<!-- ignore -->에서 했던 것처럼 변수 이름 앞에 `mut`를 붙이면 가변으로 만들 수 있습니다. `mut`를 추가하면 코드의 다른 부분에서 이 변수의 값이 변경될 것임을 미래의 코드 읽는 이에게도 명확히 전달할 수 있습니다.

예를 들어, _src/main.rs_를 아래와 같이 변경해 봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/src/main.rs}}
```

이제 프로그램을 실행하면 다음과 같은 결과가 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/output.txt}}
```

`mut`를 사용하면 `x`에 바인딩된 값을 `5`에서 `6`으로 변경할 수 있습니다. 궁극적으로 가변성을 사용할지 여부는 여러분의 선택이며, 상황에 따라 가장 명확하다고 생각하는 방식을 선택하면 됩니다.

### 상수

불변 변수와 마찬가지로, _상수_도 이름에 값을 바인딩하며 변경할 수 없습니다. 하지만 상수와 변수 사이에는 몇 가지 차이점이 있습니다.

첫째, 상수에는 `mut`를 사용할 수 없습니다. 상수는 기본적으로 불변일 뿐만 아니라, 항상 불변입니다. 상수는 `let` 대신 `const` 키워드로 선언하며, 값의 타입을 _반드시_ 명시해야 합니다. 타입과 타입 명시에 대해서는 다음 섹션 [“데이터 타입”][data-types]<!-- ignore -->에서 다룰 것이니, 지금은 자세히 신경 쓰지 않아도 됩니다. 단지 항상 타입을 명시해야 한다는 점만 기억하세요.

상수는 전역 스코프를 포함한 모든 스코프에서 선언할 수 있습니다. 이는 여러 코드 부분에서 알아야 하는 값을 지정할 때 유용합니다.

마지막 차이점은, 상수는 반드시 상수 표현식으로만 설정할 수 있으며, 런타임에만 계산될 수 있는 값으로는 설정할 수 없습니다.

상수 선언 예시는 다음과 같습니다:

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

상수 이름은 `THREE_HOURS_IN_SECONDS`이고, 값은 60(1분의 초) × 60(1시간의 분) × 3(이 프로그램에서 세고 싶은 시간)입니다. 러스트의 상수 이름 관례는 모든 글자를 대문자로 하고, 단어 사이에 언더스코어를 넣는 것입니다. 컴파일러는 제한된 연산 집합을 컴파일 타임에 평가할 수 있으므로, 이 값을 10,800으로 직접 지정하는 대신 더 이해하기 쉽고 검증하기 쉬운 방식으로 작성할 수 있습니다. 상수 선언 시 사용할 수 있는 연산에 대해서는 [러스트 레퍼런스의 상수 평가 섹션][const-eval]을 참고하세요.

상수는 선언된 스코프 내에서 프로그램이 실행되는 내내 유효합니다. 이 특성 덕분에, 여러 코드 부분에서 알아야 하는 값(예: 게임에서 플레이어가 얻을 수 있는 최대 점수, 빛의 속도 등)을 지정할 때 상수가 유용합니다.

프로그램 전체에서 하드코딩된 값을 상수로 이름 붙여 사용하면, 미래의 코드 유지보수자에게 그 값의 의미를 전달하는 데 도움이 됩니다. 또한, 하드코딩된 값을 변경해야 할 때 코드의 한 곳만 수정하면 되므로 편리합니다.

### 섀도잉

[2장][comparing-the-guess-to-the-secret-number]<!-- ignore -->의 숫자 맞추기 게임 튜토리얼에서 본 것처럼, 이전 변수와 같은 이름으로 새 변수를 선언할 수 있습니다. 러스타시안들은 첫 번째 변수가 두 번째 변수에 의해 _섀도잉(shadowed)_된다고 표현합니다. 즉, 변수 이름을 사용할 때 컴파일러가 두 번째 변수를 참조하게 됩니다. 두 번째 변수가 첫 번째 변수를 가려서, 해당 이름을 사용하는 모든 곳에서 두 번째 변수가 사용됩니다. 이는 두 번째 변수도 섀도잉되거나 스코프가 끝날 때까지 계속됩니다. 섀도잉은 같은 변수 이름과 `let` 키워드를 반복해서 사용하면 됩니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/src/main.rs}}
```

이 프로그램은 먼저 `x`에 값 `5`를 바인딩합니다. 그 다음, `let x =`를 반복하여 원래 값에 `1`을 더해 `x`의 값이 `6`이 됩니다. 그 다음, 중괄호로 만든 내부 스코프에서 세 번째 `let` 문이 또다시 `x`를 섀도잉하여 이전 값에 `2`를 곱해 `x`의 값이 `12`가 됩니다. 스코프가 끝나면 내부 섀도잉이 종료되고, `x`는 다시 `6`이 됩니다. 프로그램을 실행하면 다음과 같은 결과가 나옵니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/output.txt}}
```

섀도잉은 변수에 `mut`를 붙이는 것과 다릅니다. 만약 `let` 키워드 없이 변수를 재할당하려고 하면 컴파일 타임 오류가 발생합니다. `let`을 사용하면 값에 여러 변환을 적용한 뒤, 변환이 끝난 후에는 변수를 불변으로 유지할 수 있습니다.

`mut`와 섀도잉의 또 다른 차이점은, `let` 키워드를 다시 사용하면 사실상 새 변수를 만드는 것이므로, 값의 타입을 변경하면서도 같은 이름을 재사용할 수 있다는 점입니다. 예를 들어, 프로그램에서 사용자에게 텍스트 사이에 몇 개의 공백을 넣을지 물어보고, 그 입력을 숫자로 저장하고 싶다고 합시다:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-04-shadowing-can-change-types/src/main.rs:here}}
```

첫 번째 `spaces` 변수는 문자열 타입이고, 두 번째 `spaces` 변수는 숫자 타입입니다. 섀도잉을 사용하면 `spaces_str`과 `spaces_num`처럼 다른 이름을 고민할 필요 없이, 더 간단한 `spaces` 이름을 재사용할 수 있습니다. 하지만 만약 `mut`를 사용해서 타입을 변경하려고 하면, 아래와 같이 컴파일 타임 오류가 발생합니다:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/src/main.rs:here}}
```

에러 메시지는 변수의 타입을 변경할 수 없다고 알려줍니다:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/output.txt}}
```

이제 변수가 어떻게 동작하는지 살펴봤으니, 변수에 사용할 수 있는 다양한 데이터 타입을 알아봅시다.

[comparing-the-guess-to-the-secret-number]: ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[data-types]: ch03-02-data-types.html#data-types
[storing-values-with-variables]: ch02-00-guessing-game-tutorial.html#storing-values-with-variables
[const-eval]: ../reference/const_eval.html
