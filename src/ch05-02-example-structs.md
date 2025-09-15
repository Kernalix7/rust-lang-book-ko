## 구조체를 사용하는 예제 프로그램

구조체를 언제 사용하면 좋은지 이해하기 위해, 사각형의 넓이를 계산하는 프로그램을 작성해 봅시다. 먼저 단일 변수만 사용해서 시작한 뒤, 코드를 리팩터링하여 구조체를 사용하는 방식으로 바꿔보겠습니다.

픽셀 단위로 지정된 사각형의 너비와 높이를 받아 넓이를 계산하는 _rectangles_라는 새 바이너리 프로젝트를 Cargo로 만들어 봅시다. 리스트 5-8은 _src/main.rs_에 작성할 수 있는 간단한 프로그램 예시입니다.

<Listing number="5-8" file-name="src/main.rs" caption="너비와 높이 변수를 따로 지정하여 사각형의 넓이 계산하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:all}}
```

</Listing>

이제 `cargo run`으로 프로그램을 실행해 보세요:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/output.txt}}
```

이 코드는 각 변수를 `area` 함수에 전달하여 사각형의 넓이를 계산하는 데 성공합니다. 하지만 코드를 더 명확하고 읽기 쉽게 만들 수 있습니다.

이 코드의 문제점은 `area` 함수의 시그니처에서 드러납니다:

```rust,ignore
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:here}}
```

`area` 함수는 하나의 사각형의 넓이를 계산해야 하지만, 우리가 작성한 함수는 두 개의 매개변수를 받으며, 이 매개변수들이 서로 관련되어 있다는 점이 프로그램 어디에도 명확하게 드러나지 않습니다. 너비와 높이를 함께 묶는 것이 더 읽기 쉽고 관리하기도 쉬울 것입니다. 3장 [“튜플 타입”][the-tuple-type]<!-- ignore -->에서 이미 한 가지 방법을 논의했습니다: 튜플을 사용하는 것입니다.

### 튜플로 리팩터링하기

리스트 5-9는 튜플을 사용하는 또 다른 버전의 프로그램을 보여줍니다.

<Listing number="5-9" file-name="src/main.rs" caption="튜플로 사각형의 너비와 높이 지정하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-09/src/main.rs}}
```

</Listing>

이 프로그램은 한 가지 면에서는 더 나아졌습니다. 튜플을 사용하면 구조가 조금 더 생기고, 이제 하나의 인자만 전달하면 됩니다. 하지만 다른 면에서는 덜 명확해졌습니다. 튜플은 각 요소에 이름이 없으므로, 튜플의 각 부분에 인덱스로 접근해야 하며, 계산이 덜 명확해집니다.

넓이 계산에서는 너비와 높이를 뒤바꿔도 상관없지만, 만약 화면에 사각형을 그려야 한다면 순서가 중요해집니다! 너비가 튜플 인덱스 `0`, 높이가 인덱스 `1`임을 기억해야 합니다. 다른 사람이 코드를 사용할 때는 이 점을 더 어렵게 파악하고 기억해야 할 것입니다. 데이터의 의미를 코드에 전달하지 않았기 때문에, 오류가 발생하기 쉬워졌습니다.

### 구조체로 리팩터링: 더 많은 의미 부여하기

구조체를 사용하면 데이터에 이름을 붙여 의미를 더할 수 있습니다. 튜플을 구조체로 변환하여 전체에 이름을 붙이고, 각 부분에도 이름을 붙일 수 있습니다. 리스트 5-10을 참고하세요.

<Listing number="5-10" file-name="src/main.rs" caption="`Rectangle` 구조체 정의하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-10/src/main.rs}}
```

</Listing>

여기서는 `Rectangle`이라는 구조체를 정의했습니다. 중괄호 안에는 `width`와 `height` 필드를 정의했으며, 둘 다 타입은 `u32`입니다. 그리고 `main`에서 너비가 `30`, 높이가 `50`인 `Rectangle` 인스턴스를 만들었습니다.

`area` 함수는 이제 하나의 매개변수만 받으며, 이름은 `rectangle`이고 타입은 구조체 `Rectangle`의 불변 참조입니다. 4장에서 언급했듯이, 구조체의 소유권을 가져가지 않고 빌림만 하도록 했습니다. 이렇게 하면 `main`이 `rect1`의 소유권을 계속 유지할 수 있으므로, 함수 시그니처와 함수 호출에서 `&`를 사용합니다.

`area` 함수는 `Rectangle` 인스턴스의 `width`와 `height` 필드에 접근합니다(빌린 구조체 인스턴스의 필드에 접근해도 값이 이동되지 않으므로, 구조체를 빌려 사용하는 경우가 많습니다). 이제 `area` 함수의 시그니처는 우리가 의도한 바를 정확히 표현합니다: `Rectangle`의 넓이를 계산하며, 그때 `width`와 `height` 필드를 사용합니다. 너비와 높이가 서로 관련되어 있음을 코드로 전달하고, 튜플의 인덱스 `0`과 `1` 대신 의미 있는 이름을 사용할 수 있습니다. 명확성이 크게 향상되었습니다.

### 파생 트레이트로 유용한 기능 추가하기

디버깅 중에 `Rectangle` 인스턴스의 모든 필드 값을 출력할 수 있으면 유용할 것입니다. 리스트 5-11은 이전 장에서 사용한 [`println!` 매크로][println]<!-- ignore -->를 사용해 보려는 시도를 보여줍니다. 하지만 이 코드는 동작하지 않습니다.

<Listing number="5-11" file-name="src/main.rs" caption="`Rectangle` 인스턴스 출력 시도하기">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/src/main.rs}}
```

</Listing>

이 코드를 컴파일하면 다음과 같은 핵심 에러가 발생합니다:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:3}}
```

`println!` 매크로는 다양한 포맷팅을 지원하며, 기본적으로 중괄호는 `Display`라는 포맷팅을 사용합니다. 이는 최종 사용자에게 직접 보여줄 출력에 적합합니다. 지금까지 본 원시 타입들은 기본적으로 `Display`를 구현하고 있는데, `1`이나 다른 원시 타입을 사용자에게 보여줄 때는 한 가지 방식만 있으면 되기 때문입니다. 하지만 구조체의 경우, 출력 포맷 방식이 다양할 수 있습니다: 쉼표를 넣을지, 중괄호를 출력할지, 모든 필드를 보여줄지 등 여러 가지가 있습니다. 이런 모호함 때문에 러스트는 우리가 원하는 방식을 추측하지 않고, 구조체에는 기본적으로 `Display` 구현을 제공하지 않습니다.

에러 메시지를 계속 읽다 보면 다음과 같은 유용한 안내를 볼 수 있습니다:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:9:10}}
```

한번 시도해 봅시다! 이제 `println!` 매크로 호출은 `println!("rect1 is {rect1:?}");`처럼 보일 것입니다. 중괄호 안에 `:?`를 넣으면 `Debug`라는 출력 포맷을 사용하라는 뜻입니다. `Debug` 트레이트를 사용하면 구조체의 값을 개발자가 디버깅할 때 유용하게 출력할 수 있습니다.

이 변경을 적용한 코드를 컴파일하면, 아직도 에러가 발생합니다:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:3}}
```

하지만 또다시 컴파일러가 유용한 안내를 제공합니다:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:9:10}}
```

러스트에는 디버깅 정보를 출력하는 기능이 있지만, 우리가 직접 해당 기능을 활성화해야 합니다. 이를 위해 구조체 정의 바로 앞에 `#[derive(Debug)]`라는 외부 속성을 추가하면 됩니다. 리스트 5-12를 참고하세요.

<Listing number="5-12" file-name="src/main.rs" caption="`Debug` 트레이트를 파생시키는 속성 추가 및 디버그 포맷으로 `Rectangle` 인스턴스 출력하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/src/main.rs}}
```

</Listing>

이제 프로그램을 실행하면 에러 없이 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/output.txt}}
```

깔끔한 출력은 아니지만, 인스턴스의 모든 필드 값을 보여주므로 디버깅에는 확실히 도움이 됩니다. 구조체가 더 커지면, 더 읽기 쉬운 출력이 필요할 수 있습니다. 그럴 때는 `println!` 문자열에서 `{:?}` 대신 `{:#?}`를 사용하면 됩니다. 이 예제에서 `{:#?}` 스타일을 사용하면 다음과 같은 출력이 나옵니다:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-02-pretty-debug/output.txt}}
```

`Debug` 포맷을 사용해 값을 출력하는 또 다른 방법은 [`dbg!` 매크로][dbg]<!-- ignore -->를 사용하는 것입니다. 이 매크로는 표현식의 소유권을 가져가서(반면 `println!`은 참조만 받음), 코드에서 해당 `dbg!` 매크로가 호출된 파일과 줄 번호, 그리고 표현식의 결과값을 출력한 뒤, 그 값의 소유권을 반환합니다.

> 참고: `dbg!` 매크로는 표준 에러 콘솔 스트림(`stderr`)에 출력합니다. 반면 `println!`은 표준 출력 콘솔 스트림(`stdout`)에 출력합니다. `stderr`와 `stdout`에 대해서는 [12장 "표준 출력 대신 표준 에러로 에러 메시지 작성하기"][err]<!-- ignore -->에서 더 다룹니다.

아래는 `width` 필드에 할당되는 값과 `rect1` 전체 구조체의 값을 확인하고 싶을 때의 예시입니다:

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/src/main.rs}}
```

`dbg!`를 `30 * scale` 표현식에 감싸면, `dbg!`가 표현식의 값을 소유권과 함께 반환하므로, `width` 필드는 `dbg!`가 없을 때와 동일한 값을 갖게 됩니다. `rect1`의 소유권을 `dbg!`에 넘기고 싶지 않으므로, 다음 호출에서는 `rect1`에 대한 참조를 사용합니다. 이 예제의 출력은 다음과 같습니다:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/output.txt}}
```

첫 번째 출력은 _src/main.rs_ 10번째 줄에서 `30 * scale`을 디버깅한 결과이며, 값은 `60`입니다(정수의 `Debug` 포맷은 값만 출력함). _src/main.rs_ 14번째 줄의 `dbg!` 호출은 `&rect1`의 값을 출력하며, 이는 `Rectangle` 구조체입니다. 이 출력은 `Rectangle` 타입의 예쁜 `Debug` 포맷을 사용합니다. `dbg!` 매크로는 코드가 어떻게 동작하는지 파악할 때 정말 유용합니다!

`Debug` 트레이트 외에도, 러스트는 `derive` 속성과 함께 사용할 수 있는 여러 트레이트를 제공합니다. 이 트레이트와 동작 목록은 [부록 C][app-c]<!-- ignore -->에 있습니다. 10장에서는 이러한 트레이트를 커스텀 동작으로 구현하는 방법과, 직접 트레이트를 만드는 방법을 다룹니다. `derive` 외에도 다양한 속성이 있으니, 자세한 내용은 [러스트 레퍼런스의 "속성" 섹션][attributes]을 참고하세요.

우리의 `area` 함수는 매우 구체적입니다. 사각형의 넓이만 계산합니다. 이 동작을 `Rectangle` 구조체와 더 밀접하게 연결하면 더 좋을 것입니다. 다른 타입에서는 동작하지 않으니까요. 이제 `area` 함수를 `Rectangle` 타입에 정의된 _메서드_로 리팩터링하는 방법을 살펴봅시다.

[the-tuple-type]: ch03-02-data-types.html#the-tuple-type
[app-c]: appendix-03-derivable-traits.md
[println]: ../std/macro.println.html
[dbg]: ../std/macro.dbg.html
[err]: ch12-06-writing-to-stderr-instead-of-stdout.html
[attributes]: ../reference/attributes.html