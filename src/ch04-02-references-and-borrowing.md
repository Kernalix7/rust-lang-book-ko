## 참조와 빌림

리스트 4-5의 튜플 코드에서 문제는, `calculate_length` 함수에 `String`을 넘기면 소유권이 이동하므로, 함수 호출 후에도 `String`을 계속 사용하려면 반환값으로 다시 받아야 한다는 점입니다. 대신, `String` 값에 대한 참조를 제공할 수 있습니다. _참조(reference)_는 포인터와 비슷하게, 해당 주소를 따라가면 그 위치에 저장된 데이터를 접근할 수 있게 해줍니다. 그 데이터는 다른 변수가 소유합니다. 포인터와 달리, 참조는 해당 참조가 유효한 동안 반드시 올바른 타입의 값을 가리키도록 보장됩니다.

아래는 값을 소유하지 않고 객체에 대한 참조를 매개변수로 받는 `calculate_length` 함수의 정의와 사용 예시입니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:all}}
```

</Listing>

먼저, 변수 선언과 함수 반환값에 있던 튜플 코드가 모두 사라졌음을 확인하세요. 그리고 `calculate_length`에 `&s1`을 넘기고, 함수 시그니처에서 `&String`을 받는다는 점에 주목하세요. 앰퍼샌드(`&`)는 _참조_를 의미하며, 소유권을 가져가지 않고 값을 참조할 수 있게 해줍니다. 그림 4-6은 이 개념을 시각적으로 보여줍니다.

<img alt="Three tables: the table for s contains only a pointer to the table
for s1. The table for s1 contains the stack data for s1 and points to the
string data on the heap." src="img/trpl04-06.svg" class="center" />

<span class="caption">그림 4-6: `&String s`가 `String s1`을 가리키는 다이어그램</span>

> 참고: `&`로 참조하는 것의 반대는 _역참조(dereferencing)_입니다. 역참조는 `*` 연산자로 수행합니다. 8장에서 역참조 연산자 사용 예시를, 15장에서 역참조의 세부 내용을 다룹니다.

함수 호출 부분을 자세히 살펴봅시다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:here}}
```

`&s1` 문법은 `s1`의 값을 _참조_하지만, 소유권은 가져가지 않습니다. 참조는 소유권을 가지지 않으므로, 참조가 더 이상 사용되지 않아도 참조 대상 값은 삭제되지 않습니다.

함수 시그니처에서도 `&`를 사용하여 매개변수 `s`가 참조 타입임을 나타냅니다. 설명 주석을 추가하면 다음과 같습니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-08-reference-with-annotations/src/main.rs:here}}
```

변수 `s`의 유효 스코프는 일반적인 함수 매개변수와 동일하지만, 참조가 가리키는 값은 `s`가 사용되지 않게 되어도 삭제되지 않습니다. 왜냐하면 `s`가 소유권을 가지지 않기 때문입니다. 함수가 값을 직접 받는 대신 참조를 받으면, 소유권을 반환할 필요가 없습니다. 애초에 소유권을 가져가지 않았으니까요.

참조를 만드는 행위를 _빌림(borrowing)_이라고 부릅니다. 실제 생활에서 누군가가 어떤 것을 소유하고 있으면, 우리는 그것을 빌릴 수 있습니다. 다 쓰고 나면 돌려줘야 하죠. 소유권은 없습니다.

그렇다면, 빌린 값을 수정하려고 하면 어떻게 될까요? 리스트 4-6의 코드를 실행해 보세요. 미리 말씀드리면, 동작하지 않습니다!

<Listing number="4-6" file-name="src/main.rs" caption="빌린 값을 수정하려고 시도하기">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-06/src/main.rs}}
```

</Listing>

에러 메시지는 다음과 같습니다:

```console
{{#include ../listings/ch04-understanding-ownership/listing-04-06/output.txt}}
```

변수가 기본적으로 불변인 것처럼, 참조도 기본적으로 불변입니다. 참조한 값을 수정할 수 없습니다.

### 가변 참조

리스트 4-6의 코드를 약간만 수정하면, 빌린 값을 수정할 수 있습니다. _가변 참조(mutable reference)_를 사용하면 됩니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-09-fixes-listing-04-06/src/main.rs}}
```

</Listing>

먼저 `s`를 `mut`로 선언합니다. 그리고 `change` 함수를 호출할 때 `&mut s`로 가변 참조를 넘기고, 함수 시그니처도 `some_string: &mut String`으로 가변 참조를 받도록 수정합니다. 이렇게 하면 `change` 함수가 빌린 값을 변경할 것임을 명확하게 알릴 수 있습니다.

가변 참조에는 중요한 제약이 있습니다: 어떤 값에 가변 참조가 하나 있으면, 동시에 다른 참조를 만들 수 없습니다. 아래 코드는 같은 값에 두 개의 가변 참조를 만들려고 하므로 실패합니다:

<Listing file-name="src/main.rs">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/src/main.rs:here}}
```

</Listing>

에러 메시지는 다음과 같습니다:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/output.txt}}
```

이 에러는 동시에 두 개의 가변 참조를 만들 수 없다는 뜻입니다. 첫 번째 가변 참조는 `r1`이고, `println!`에서 사용될 때까지 유효합니다. 그런데 그 사이에 같은 데이터를 가리키는 두 번째 가변 참조 `r2`를 만들려고 했기 때문에 에러가 발생합니다.

동시에 여러 개의 가변 참조를 허용하지 않는 제약 덕분에, 변경은 매우 통제된 방식으로만 가능합니다. 대부분의 언어에서는 언제든 값을 변경할 수 있으므로, 러스트의 이 제약이 처음에는 낯설게 느껴질 수 있습니다. 하지만 이 제약 덕분에 러스트는 _데이터 레이스(data race)_를 컴파일 타임에 방지할 수 있습니다. 데이터 레이스란 다음 세 가지 조건이 동시에 발생할 때 생깁니다:

- 두 개 이상의 포인터가 동시에 같은 데이터를 접근한다.
- 그 중 하나 이상의 포인터가 데이터를 변경한다.
- 데이터를 접근하는 동기화 메커니즘이 없다.

데이터 레이스는 정의되지 않은 동작을 유발하며, 런타임에 추적하고 수정하기 매우 어렵습니다. 러스트는 데이터 레이스가 있는 코드를 컴파일하지 않음으로써 이 문제를 방지합니다!

항상 그렇듯, 중괄호로 새로운 스코프를 만들면 동시에 여러 가변 참조를 만들 수 있습니다. 단, _동시에_ 존재하지 않을 때만 가능합니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-11-muts-in-separate-scopes/src/main.rs:here}}
```

러스트는 가변 참조와 불변 참조를 동시에 만드는 것도 금지합니다. 아래 코드는 에러가 발생합니다:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/src/main.rs:here}}
```

에러 메시지는 다음과 같습니다:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/output.txt}}
```

즉, 불변 참조가 있는 동안에는 가변 참조를 만들 수 없습니다.

불변 참조를 사용하는 사람은 값이 갑자기 바뀌지 않을 것이라고 기대합니다! 하지만 여러 개의 불변 참조는 허용됩니다. 데이터를 읽기만 하는 경우에는, 다른 사람이 읽는 데 영향을 주지 않으니까요.

참조의 스코프는 참조가 도입된 시점부터 마지막으로 사용되는 시점까지입니다. 예를 들어, 아래 코드는 컴파일됩니다. 불변 참조의 마지막 사용이 `println!`에서 끝나고, 그 뒤에 가변 참조가 도입되기 때문입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-13-reference-scope-ends/src/main.rs:here}}
```

불변 참조 `r1`과 `r2`의 스코프는 마지막으로 사용된 `println!` 이후 끝나며, 그 뒤에 가변 참조 `r3`가 만들어집니다. 두 참조의 스코프가 겹치지 않으므로, 이 코드는 허용됩니다. 컴파일러는 참조가 더 이상 사용되지 않는 시점을 파악할 수 있습니다.

빌림 관련 에러가 때로는 답답할 수 있지만, 러스트 컴파일러가 잠재적인 버그를 미리(런타임이 아니라 컴파일 타임에) 알려주고, 문제 위치를 정확히 알려준다는 점을 기억하세요. 덕분에 데이터가 예상과 다르게 동작하는 원인을 직접 추적할 필요가 없습니다.

### 댕글링 참조

포인터를 사용하는 언어에서는 _댕글링 포인터(dangling pointer)_—이미 다른 곳에 할당된 메모리 위치를 참조하는 포인터—를 실수로 만들기 쉽습니다. 예를 들어, 메모리를 해제한 뒤에도 그 위치를 가리키는 포인터가 남아 있을 수 있습니다. 러스트에서는 컴파일러가 참조가 댕글링되지 않도록 보장합니다. 즉, 참조가 어떤 데이터를 가리키고 있다면, 그 데이터가 참조보다 먼저 스코프를 벗어나지 않도록 컴파일러가 검사합니다.

댕글링 참조를 만들려고 하면, 러스트가 컴파일 타임 에러로 막아줍니다:

<Listing file-name="src/main.rs">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/src/main.rs}}
```

</Listing>

에러 메시지는 다음과 같습니다:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/output.txt}}
```

이 에러 메시지는 아직 다루지 않은 기능인 _수명(lifetime)_을 언급합니다. 수명에 대해서는 10장에서 자세히 다룹니다. 수명 부분을 제외하고 보면, 메시지의 핵심은 다음과 같습니다:

```text
this function's return type contains a borrowed value, but there is no value
for it to be borrowed from
```

`dangle` 코드의 각 단계에서 무슨 일이 일어나는지 자세히 살펴봅시다:

<Listing file-name="src/main.rs">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-15-dangling-reference-annotated/src/main.rs:here}}
```

</Listing>

`s`는 `dangle` 함수 내부에서 생성되므로, 함수가 끝나면 `s`는 해제됩니다. 그런데 우리는 그 값을 참조로 반환하려 했습니다. 이 참조는 더 이상 유효하지 않은 `String`을 가리키게 됩니다. 이는 잘못된 동작입니다! 러스트는 이런 코드를 허용하지 않습니다.

해결 방법은 값을 직접 반환하는 것입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-16-no-dangle/src/main.rs:here}}
```

이 코드는 아무 문제 없이 동작합니다. 소유권이 함수 밖으로 이동하며, 해제되지 않습니다.

### 참조의 규칙

지금까지 참조에 대해 배운 내용을 정리해 봅시다:

- 한 시점에 _가변 참조는 하나만_ 또는 _불변 참조는 여러 개_만 가질 수 있다.
- 참조는 항상 유효해야 한다.

다음으로, 또 다른 종류의 참조인 슬라이스를 살펴봅시다.
