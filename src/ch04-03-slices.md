## 슬라이스 타입

_슬라이스(slice)_는 [컬렉션](ch08-00-common-collections.md)<!-- ignore -->에서 연속된 요소 집합을 참조할 수 있게 해줍니다. 슬라이스는 참조의 일종이므로, 소유권을 가지지 않습니다.

간단한 프로그래밍 문제를 생각해 봅시다: 공백으로 구분된 단어들로 이루어진 문자열을 받아, 그 문자열에서 첫 번째 단어를 반환하는 함수를 작성하세요. 만약 문자열에 공백이 없다면, 전체 문자열이 하나의 단어이므로 전체 문자열을 반환해야 합니다.

> 참고: 이 섹션에서는 문자열 슬라이스를 소개하기 위해 ASCII만 다룹니다. UTF-8 처리에 대한 더 자세한 내용은 [8장 "UTF-8 인코딩된 텍스트를 문자열로 저장하기"][strings]<!-- ignore -->에서 다룹니다.

슬라이스 없이 이 함수의 시그니처를 어떻게 작성할지 고민해 봅시다. 슬라이스가 해결해 줄 문제를 이해하기 위해서입니다:

```rust,ignore
fn first_word(s: &String) -> ?
```

`first_word` 함수는 `&String` 타입의 매개변수를 받습니다. 소유권이 필요 없으므로 괜찮습니다. (러스트의 관례는 함수가 소유권이 필요하지 않으면 소유권을 받지 않는다는 점입니다. 이 이유는 앞으로 더 명확해질 것입니다.) 하지만 반환값은 어떻게 해야 할까요? 문자열의 _일부_를 반환하는 방법이 없습니다. 대신, 공백의 인덱스를 반환할 수 있습니다. 리스트 4-7을 참고하세요.

<Listing number="4-7" file-name="src/main.rs" caption="`first_word` 함수가 `String` 매개변수의 바이트 인덱스 값을 반환하는 예시">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:here}}
```

</Listing>

`String`을 바이트 배열로 변환하여 각 요소를 검사해야 하므로, `as_bytes` 메서드를 사용합니다.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:as_bytes}}
```

다음으로, 바이트 배열에 대해 `iter` 메서드로 이터레이터를 만듭니다:

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:iter}}
```

이터레이터에 대해서는 [13장][ch13]<!-- ignore -->에서 더 자세히 다룹니다. 지금은 `iter`가 컬렉션의 각 요소를 반환한다는 점만 기억하세요. `enumerate`는 이터레이터의 결과를 튜플로 감싸서, 각 요소의 인덱스와 해당 요소에 대한 참조를 반환합니다. 튜플의 첫 번째 요소는 인덱스, 두 번째 요소는 요소의 참조입니다. 직접 인덱스를 계산하는 것보다 편리합니다.

`enumerate`가 튜플을 반환하므로, 패턴을 사용해 튜플을 분해할 수 있습니다. 패턴에 대해서는 [6장][ch6]<!-- ignore -->에서 더 다룹니다. `for` 루프에서는 인덱스는 `i`, 바이트는 `&item`으로 패턴을 지정합니다. `.iter().enumerate()`에서 요소의 참조를 받으므로, 패턴에 `&`를 붙입니다.

루프 내부에서는 바이트 리터럴 문법을 사용해 공백 바이트를 찾습니다. 공백을 찾으면 해당 위치를 반환하고, 그렇지 않으면 `s.len()`으로 문자열의 길이를 반환합니다.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:inside_for}}
```

이제 문자열에서 첫 번째 단어의 끝 인덱스를 찾을 수 있게 되었습니다. 하지만 문제가 있습니다. 반환값이 `usize`이므로, 이 값은 `&String`과 연관된 맥락에서만 의미가 있습니다. 즉, `String`과 별개의 값이므로, 미래에도 여전히 유효하다는 보장이 없습니다. 리스트 4-8의 프로그램을 보면, 리스트 4-7의 `first_word` 함수를 사용한 뒤, `String`의 내용을 변경합니다.

<Listing number="4-8" file-name="src/main.rs" caption="`first_word` 함수의 결과를 저장한 뒤 `String` 내용을 변경하는 예시">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-08/src/main.rs:here}}
```

</Listing>

이 프로그램은 에러 없이 컴파일되며, `s.clear()` 이후에도 `word`를 사용할 수 있습니다. `word`는 여전히 값 `5`를 가지고 있습니다. 이 값을 `s`와 함께 사용해 첫 번째 단어를 추출하려 하면, 버그가 발생합니다. 왜냐하면 `s`의 내용이 변경된 뒤에도 `word`는 이전 인덱스를 그대로 가지고 있기 때문입니다.

이처럼 `word`의 인덱스가 `s`의 데이터와 동기화되지 않는 문제를 관리하는 것은 번거롭고 오류가 발생하기 쉽습니다. 만약 `second_word` 함수를 작성한다면, 시그니처는 다음과 같아야 합니다:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

이제 시작 인덱스와 끝 인덱스를 모두 추적해야 하며, 데이터의 특정 상태에서 계산된 값이 데이터와 연결되어 있지 않습니다. 서로 연관 없는 세 개의 변수를 계속 동기화해야 합니다.

다행히도, 러스트에는 이 문제를 해결하는 기능이 있습니다: 문자열 슬라이스입니다.

### 문자열 슬라이스

_문자열 슬라이스(string slice)_는 `String`의 연속된 일부 요소를 참조하는 것으로, 아래와 같이 작성합니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-17-slice/src/main.rs:here}}
```

전체 `String`에 대한 참조 대신, `hello`는 `String`의 일부를 참조합니다. `[0..5]` 부분이 슬라이스의 범위를 지정합니다. 슬라이스는 대괄호 안에 `[시작 인덱스..끝 인덱스]` 형태로 작성합니다. _시작 인덱스_는 슬라이스의 첫 번째 위치이고, _끝 인덱스_는 마지막 위치보다 1 큰 값입니다. 내부적으로 슬라이스는 시작 위치와 길이를 저장합니다. 예를 들어, `let world = &s[6..11];`에서 `world`는 `s`의 6번 인덱스부터 5개의 바이트를 가리키는 슬라이스입니다.

아래 그림 4-7은 이를 시각적으로 보여줍니다.

<img alt="Three tables: a table representing the stack data of s, which points
to the byte at index 0 in a table of the string data &quot;hello world&quot; on
the heap. The third table rep-resents the stack data of the slice world, which
has a length value of 5 and points to byte 6 of the heap data table."
src="img/trpl04-07.svg" class="center" style="width: 50%;" />

<span class="caption">그림 4-7: `String`의 일부를 참조하는 문자열 슬라이스</span>

러스트의 `..` 범위 문법을 활용하면, 0번 인덱스부터 시작할 때는 앞의 값을 생략할 수 있습니다. 즉, 아래 두 코드는 동일합니다:

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

마찬가지로, 슬라이스가 문자열의 마지막 바이트까지 포함한다면, 끝 인덱스도 생략할 수 있습니다. 즉, 아래 두 코드는 동일합니다:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

두 값 모두 생략하면 전체 문자열의 슬라이스가 됩니다. 즉, 아래 두 코드는 동일합니다:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

> 참고: 문자열 슬라이스의 범위 인덱스는 반드시 유효한 UTF-8 문자 경계여야 합니다. 만약 멀티바이트 문자 중간에서 슬라이스를 만들려고 하면, 프로그램은 에러와 함께 종료됩니다.

이제 이 정보를 바탕으로, `first_word`를 슬라이스를 반환하도록 다시 작성해 봅시다. 문자열 슬라이스 타입은 `&str`로 표기합니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-18-first-word-slice/src/main.rs:here}}
```

</Listing>

공백의 인덱스를 찾는 방식은 이전과 동일합니다. 공백을 찾으면, 문자열의 시작부터 공백 인덱스까지 슬라이스를 반환합니다.

이제 `first_word`를 호출하면, 데이터와 연결된 단일 값을 반환받게 됩니다. 반환값은 슬라이스의 시작 위치와 길이로 구성됩니다.

슬라이스를 반환하는 방식은 `second_word` 함수에도 적용할 수 있습니다:

```rust,ignore
fn second_word(s: &String) -> &str {
```

이제 API가 훨씬 명확해졌으며, 컴파일러가 슬라이스가 참조하는 데이터가 유효한지 보장해 줍니다. 리스트 4-8의 버그를 기억하세요. 첫 번째 단어의 끝 인덱스를 얻은 뒤, 문자열을 비우면 인덱스가 무효화됩니다. 이 코드는 논리적으로 잘못되었지만, 즉시 에러가 발생하지는 않습니다. 슬라이스를 사용하면 이런 버그가 컴파일 타임에 바로 잡힙니다. 슬라이스 버전의 `first_word`를 사용하면 컴파일 타임 에러가 발생합니다:

<Listing file-name="src/main.rs">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/src/main.rs:here}}
```

</Listing>

컴파일러 에러는 다음과 같습니다:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/output.txt}}
```

빌림 규칙을 기억하세요. 불변 참조가 있으면, 가변 참조를 동시에 만들 수 없습니다. `clear`는 `String`을 변경해야 하므로, 가변 참조가 필요합니다. `println!`에서 `word`의 참조를 사용하므로, 불변 참조가 아직 유효합니다. 러스트는 `clear`의 가변 참조와 `word`의 불변 참조가 동시에 존재하는 것을 허용하지 않으므로, 컴파일이 실패합니다. 러스트는 API를 더 쉽게 사용할 수 있게 해줄 뿐만 아니라, 컴파일 타임에 오류를 제거해 줍니다!

<!-- Old heading. Do not remove or links may break. -->

<a id="string-literals-are-slices"></a>

#### 문자열 리터럴도 슬라이스다

앞서 문자열 리터럴이 바이너리 내부에 저장된다고 했습니다. 이제 슬라이스를 알았으니, 문자열 리터럴을 제대로 이해할 수 있습니다:

```rust
let s = "Hello, world!";
```

여기서 `s`의 타입은 `&str`입니다. 즉, 바이너리의 특정 위치를 가리키는 슬라이스입니다. 그래서 문자열 리터럴은 불변입니다. `&str`은 불변 참조이기 때문입니다.

#### 함수 매개변수로 문자열 슬라이스 사용하기

리터럴과 `String` 값 모두에서 슬라이스를 만들 수 있다는 점을 알았다면, `first_word`의 시그니처를 더 개선할 수 있습니다:

```rust,ignore
fn first_word(s: &String) -> &str {
```

러스트에 익숙한 사람이라면, 리스트 4-9처럼 시그니처를 작성할 것입니다. 이렇게 하면 `&String`과 `&str` 모두에 대해 같은 함수를 사용할 수 있습니다.

<Listing number="4-9" caption="매개변수 타입을 문자열 슬라이스로 개선한 `first_word` 함수">

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:here}}
```

</Listing>

문자열 슬라이스가 있다면 바로 넘길 수 있습니다. `String`이 있다면, `String`의 슬라이스나 참조를 넘길 수 있습니다. 이 유연성은 _역참조 강제(deref coercion)_라는 기능 덕분입니다. 역참조 강제에 대해서는 [15장 "함수와 메서드에서의 암묵적 역참조 강제"][deref-coercions]<!--ignore-->에서 다룹니다.

함수의 매개변수를 `String` 참조 대신 문자열 슬라이스로 지정하면, API가 더 일반적이고 유용해집니다. 기능은 그대로 유지됩니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:usage}}
```

</Listing>

### 다른 슬라이스 타입

문자열 슬라이스는 문자열에 특화된 기능입니다. 하지만 더 일반적인 슬라이스 타입도 있습니다. 예를 들어, 아래와 같은 배열이 있다고 합시다:

```rust
let a = [1, 2, 3, 4, 5];
```

문자열의 일부를 참조하듯, 배열의 일부를 참조할 수도 있습니다:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

이 슬라이스의 타입은 `&[i32]`입니다. 문자열 슬라이스와 마찬가지로, 첫 번째 요소의 참조와 길이를 저장합니다. 이런 슬라이스는 다양한 컬렉션에서 사용할 수 있습니다. 컬렉션에 대해서는 8장에서 벡터를 다루며 더 자세히 설명합니다.

## 요약

소유권, 빌림, 슬라이스 개념은 러스트 프로그램에서 컴파일 타임에 메모리 안전성을 보장합니다. 러스트는 다른 시스템 프로그래밍 언어처럼 메모리 사용을 직접 제어할 수 있게 해주지만, 소유자가 스코프를 벗어나면 데이터를 자동으로 정리해 주므로, 직접 코드를 작성하고 디버깅할 필요가 없습니다.

소유권은 러스트의 여러 부분에 영향을 미치므로, 앞으로도 계속 다루게 될 것입니다. 이제 5장으로 넘어가서, 여러 데이터 조각을 하나의 구조체로 묶는 방법을 배워봅시다.

[ch13]: ch13-02-iterators.html
[ch6]: ch06-02-match.html#patterns-that-bind-to-values
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[deref-coercions]: ch15-02-deref.html#implicit-deref-coercions-with-functions-and-methods