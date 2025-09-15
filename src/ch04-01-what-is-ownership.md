## 소유권이란?

_소유권(ownership)_은 러스트 프로그램이 메모리를 관리하는 방식을 규정하는 일련의 규칙입니다. 모든 프로그램은 실행 중에 컴퓨터의 메모리 사용을 관리해야 합니다. 어떤 언어는 가비지 컬렉션을 통해 프로그램 실행 중에 더 이상 사용하지 않는 메모리를 주기적으로 찾아내고, 다른 언어는 프로그래머가 직접 메모리를 할당하고 해제해야 합니다. 러스트는 세 번째 방식을 사용합니다: 메모리는 소유권 시스템과 컴파일러가 검사하는 규칙 집합을 통해 관리됩니다. 규칙을 위반하면 프로그램은 컴파일되지 않습니다. 소유권의 어떤 기능도 프로그램 실행 속도를 느리게 만들지 않습니다.

소유권은 많은 프로그래머에게 새로운 개념이므로, 익숙해지는 데 시간이 걸릴 수 있습니다. 하지만 러스트와 소유권 시스템의 규칙에 익숙해질수록, 안전하고 효율적인 코드를 자연스럽게 작성할 수 있게 됩니다. 계속 연습해 보세요!

소유권을 이해하면 러스트의 독특한 기능을 이해하는 데 튼튼한 기반을 갖추게 됩니다. 이번 장에서는 소유권을 문자열이라는 매우 흔한 데이터 구조를 중심으로 예제를 통해 배웁니다.

> ### 스택과 힙
>
> 많은 프로그래밍 언어에서는 스택과 힙에 대해 자주 생각할 필요가 없습니다. 하지만 러스트처럼 시스템 프로그래밍 언어에서는 값이 스택에 있는지 힙에 있는지가 언어의 동작과 우리가 내려야 하는 결정에 영향을 미칩니다. 소유권의 일부는 이번 장에서 스택과 힙과 관련하여 설명할 것이므로, 미리 간단히 설명하겠습니다.
>
> 스택과 힙은 런타임에 코드가 사용할 수 있는 메모리의 두 영역입니다. 구조는 서로 다릅니다. 스택은 값을 받은 순서대로 저장하고, 반대 순서로 값을 제거합니다. 이를 _후입선출(last in, first out)_이라고 합니다. 접시를 쌓는 것을 생각해 보세요: 접시를 추가할 때는 맨 위에 올리고, 필요할 때는 맨 위에서 꺼냅니다. 중간이나 아래에서 접시를 꺼내는 것은 잘 작동하지 않습니다! 데이터를 추가하는 것은 _스택에 푸시(push)_하는 것이고, 제거하는 것은 _스택에서 팝(pop)_하는 것입니다. 스택에 저장되는 모든 데이터는 크기가 고정되어 있어야 합니다. 컴파일 타임에 크기를 알 수 없거나, 크기가 변할 수 있는 데이터는 힙에 저장해야 합니다.
>
> 힙은 더 무질서합니다. 데이터를 힙에 저장하려면, 원하는 크기의 공간을 요청합니다. 메모리 할당자는 힙에서 충분히 큰 빈 공간을 찾아 사용 중으로 표시하고, 그 위치의 _포인터_를 반환합니다. 이 과정을 _힙에 할당(allocating on the heap)_이라고 하며, 줄여서 _할당(allocating)_이라고도 합니다(스택에 값을 푸시하는 것은 할당으로 간주하지 않습니다). 힙의 포인터는 크기가 고정되어 있으므로, 포인터 자체는 스택에 저장할 수 있습니다. 실제 데이터를 원할 때는 포인터를 따라가야 합니다. 식당에서 자리를 안내받는 것을 생각해 보세요. 들어가면 일행의 인원수를 말하고, 안내자가 빈 테이블을 찾아 안내합니다. 늦게 온 일행은 어디에 앉았는지 물어볼 수 있습니다.
>
> 스택에 푸시하는 것은 힙에 할당하는 것보다 빠릅니다. 할당자는 새 데이터를 저장할 위치를 찾을 필요 없이 항상 스택의 맨 위에 저장하기 때문입니다. 반면, 힙에 공간을 할당하려면 먼저 충분히 큰 빈 공간을 찾아야 하고, 다음 할당을 준비하기 위한 관리 작업도 필요합니다.
>
> 힙의 데이터에 접근하는 것은 스택의 데이터에 접근하는 것보다 일반적으로 느립니다. 포인터를 따라가야 하기 때문입니다. 현대 프로세서는 메모리 내에서 점프를 덜 할수록 더 빠릅니다. 식당에서 여러 테이블의 주문을 받을 때, 한 테이블의 주문을 모두 받고 다음 테이블로 이동하는 것이 가장 효율적입니다. 테이블 A에서 주문을 받고, B에서 받고, 다시 A로 돌아가고, 다시 B로 가는 것은 훨씬 느립니다. 마찬가지로, 프로세서는 스택처럼 데이터가 가까이 있을 때 더 효율적으로 작업할 수 있습니다.
>
> 코드가 함수를 호출하면, 함수에 전달된 값(힙 데이터의 포인터도 포함될 수 있음)과 함수의 지역 변수들이 스택에 푸시됩니다. 함수가 끝나면 이 값들은 스택에서 팝됩니다.
>
> 힙의 어떤 데이터가 어떤 코드에서 사용되고 있는지 추적하고, 중복 데이터를 최소화하며, 사용하지 않는 데이터를 정리해서 메모리가 부족해지지 않게 하는 것이 소유권이 해결하는 문제입니다. 소유권을 이해하면 스택과 힙에 대해 자주 생각할 필요는 없지만, 소유권의 주요 목적이 힙 데이터를 관리하는 것임을 알면 왜 이런 방식으로 동작하는지 이해하는 데 도움이 됩니다.

### 소유권 규칙

먼저, 소유권 규칙을 살펴봅시다. 예제를 통해 규칙을 익히면서 항상 염두에 두세요:

- 러스트의 모든 값에는 _소유자(owner)_가 있다.
- 한 번에 오직 하나의 소유자만 있을 수 있다.
- 소유자가 스코프를 벗어나면, 값은 삭제된다(drop).

### 변수의 스코프

이제 러스트의 기본 문법을 익혔으니, 예제에서는 `fn main() {` 코드를 생략합니다. 직접 따라 하려면 아래 예제를 반드시 `main` 함수 안에 넣어야 합니다. 덕분에 예제가 더 간결해지고, 핵심 내용에 집중할 수 있습니다.

소유권의 첫 번째 예제로, 변수의 _스코프(scope)_를 살펴봅시다. 스코프란 프로그램에서 항목이 유효한 범위입니다. 아래 변수 예제를 보세요:

```rust
let s = "hello";
```

`s` 변수는 문자열 리터럴을 참조합니다. 문자열 값은 프로그램 텍스트에 하드코딩되어 있습니다. 변수는 선언된 시점부터 현재 _스코프_가 끝날 때까지 유효합니다. 리스트 4-1은 변수 `s`가 유효한 위치를 주석으로 표시한 프로그램입니다.

<Listing number="4-1" caption="변수와 그 유효 스코프">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-01/src/main.rs:here}}
```

</Listing>

즉, 여기서 중요한 시점은 두 가지입니다:

- `s`가 _스코프에 들어올 때_ 유효해진다.
- 스코프를 _벗어나면_ 더 이상 유효하지 않다.

이 시점까지는 스코프와 변수의 유효 시점이 다른 언어와 비슷합니다. 이제 이 개념을 바탕으로 `String` 타입을 소개하며 더 깊이 살펴봅시다.

### `String` 타입

소유권 규칙을 설명하려면, 3장 [“데이터 타입”][data-types]<!-- ignore -->에서 다룬 것보다 더 복잡한 데이터 타입이 필요합니다. 앞서 다룬 타입들은 크기가 고정되어 있어 스택에 저장되고, 스코프가 끝나면 스택에서 팝됩니다. 다른 코드에서 같은 값을 다른 스코프에서 사용하려면 빠르고 간단하게 복사해서 독립적인 인스턴스를 만들 수 있습니다. 하지만 우리는 힙에 저장되는 데이터와, 러스트가 그 데이터를 언제 정리해야 하는지 알아야 합니다. `String` 타입이 좋은 예시입니다.

이번 장에서는 `String`의 소유권과 관련된 부분에 집중합니다. 이런 특징들은 표준 라이브러리에서 제공하는 타입이든, 여러분이 만든 타입이든 모든 복잡한 데이터 타입에 적용됩니다. `String`에 대해서는 [8장][ch8]<!-- ignore -->에서 더 자세히 다룹니다.

이미 문자열 리터럴을 본 적이 있습니다. 문자열 리터럴은 프로그램에 하드코딩된 값입니다. 문자열 리터럴은 편리하지만, 모든 상황에 적합하지는 않습니다. 한 가지 이유는 불변이라는 점입니다. 또 다른 이유는 모든 문자열 값을 코드 작성 시점에 알 수 없다는 점입니다. 예를 들어, 사용자 입력을 받아 저장하고 싶을 때가 있습니다. 이런 상황을 위해 러스트에는 두 번째 문자열 타입인 `String`이 있습니다. 이 타입은 힙에 데이터를 할당하여, 컴파일 타임에 알 수 없는 크기의 텍스트도 저장할 수 있습니다. 문자열 리터럴에서 `String`을 만들려면 `from` 함수를 사용합니다:

```rust
let s = String::from("hello");
```

`::` 연산자는 이 `from` 함수를 `String` 타입의 네임스페이스 아래에 둡니다. `string_from` 같은 이름 대신, 네임스페이스를 활용하는 방식입니다. 이 문법은 [5장 "메서드 문법"][method-syntax]<!-- ignore -->과 [7장 "모듈 트리의 항목 참조 경로"][paths-module-tree]<!-- ignore -->에서 더 자세히 다룹니다.

이런 문자열은 _가변_입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-01-can-mutate-string/src/main.rs:here}}
```

여기서 차이점은 무엇일까요? 왜 `String`은 변경할 수 있지만, 리터럴은 변경할 수 없을까요? 차이는 두 타입이 메모리를 다루는 방식에 있습니다.

### 메모리와 할당

문자열 리터럴의 경우, 컴파일 타임에 내용을 알 수 있으므로, 텍스트가 실행 파일에 직접 하드코딩됩니다. 그래서 문자열 리터럴은 빠르고 효율적입니다. 하지만 이런 특성은 불변성에서만 나옵니다. 컴파일 타임에 크기를 알 수 없거나, 실행 중에 크기가 변할 수 있는 텍스트를 바이너리에 직접 넣을 수는 없습니다.

`String` 타입은 변경 가능하고 크기가 늘어날 수 있는 텍스트를 지원하기 위해, 컴파일 타임에 알 수 없는 크기의 메모리를 힙에 할당해야 합니다. 즉:

- 런타임에 메모리 할당자에게 메모리를 요청해야 합니다.
- `String`을 다 썼을 때, 메모리를 할당자에게 반환하는 방법이 필요합니다.

첫 번째 부분은 우리가 직접 처리합니다. `String::from`을 호출하면, 내부 구현이 필요한 만큼의 메모리를 요청합니다. 이는 대부분의 프로그래밍 언어에서 공통적입니다.

하지만 두 번째 부분은 다릅니다. _가비지 컬렉터(GC)_가 있는 언어에서는 GC가 사용하지 않는 메모리를 추적하고 정리해 주므로, 우리가 신경 쓸 필요가 없습니다. GC가 없는 대부분의 언어에서는, 우리가 직접 메모리가 더 이상 필요하지 않을 때를 파악하고, 명시적으로 해제하는 코드를 작성해야 합니다. 이 작업을 정확히 처리하는 것은 오랜 시간 동안 어려운 프로그래밍 문제였습니다. 잊어버리면 메모리가 낭비되고, 너무 일찍 해제하면 잘못된 변수가 생기고, 두 번 해제하면 버그가 발생합니다. 할당과 해제를 정확히 한 번씩 짝지어야 합니다.

러스트는 다른 길을 택합니다: 소유자가 스코프를 벗어나면, 메모리가 자동으로 반환됩니다. 아래는 문자열 리터럴 대신 `String`을 사용한 스코프 예제입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-02-string-scope/src/main.rs:here}}
```

`String`이 필요로 하는 메모리를 할당자에게 반환할 자연스러운 시점은, `s`가 스코프를 벗어날 때입니다. 변수가 스코프를 벗어나면, 러스트가 특별한 함수를 자동으로 호출합니다. 이 함수는 [`drop`][drop]<!-- ignore -->이며, `String`의 작성자가 메모리를 반환하는 코드를 넣을 수 있습니다. 러스트는 닫는 중괄호에서 `drop`을 자동으로 호출합니다.

> 참고: C++에서는 이런 자원 해제 패턴을 _Resource Acquisition Is Initialization (RAII)_라고 부릅니다. 러스트의 `drop` 함수는 RAII 패턴을 사용해 본 적이 있다면 익숙할 것입니다.

이 패턴은 러스트 코드 작성 방식에 큰 영향을 줍니다. 지금은 간단해 보이지만, 더 복잡한 상황에서 여러 변수가 힙에 할당된 데이터를 사용하려고 할 때, 코드의 동작이 예상과 다를 수 있습니다. 이제 그런 상황을 살펴봅시다.

<!-- Old heading. Do not remove or links may break. -->

<a id="ways-variables-and-data-interact-move"></a>

#### 변수와 데이터의 이동(Move)

러스트에서는 여러 변수가 같은 데이터를 다양한 방식으로 다룰 수 있습니다. 정수 예제를 리스트 4-2에서 살펴봅시다.

<Listing number="4-2" caption="변수 `x`의 정수 값을 `y`에 할당하기">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-02/src/main.rs:here}}
```

</Listing>

이 코드는 "값 5를 `x`에 바인딩하고, `x`의 값을 복사해서 `y`에 바인딩한다"는 뜻입니다. 이제 `x`와 `y` 두 변수 모두 값이 5입니다. 실제로 정수는 크기가 고정된 단순 값이므로, 두 개의 5가 스택에 저장됩니다.

이번에는 `String` 버전을 살펴봅시다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-03-string-move/src/main.rs:here}}
```

겉보기에는 비슷하므로, 두 번째 줄이 `s1`의 값을 복사해서 `s2`에 바인딩한다고 생각할 수 있습니다. 하지만 실제로는 다릅니다.

아래 그림 4-1을 보면, `String`의 내부 구조를 알 수 있습니다. `String`은 세 부분으로 이루어져 있습니다: 문자열 내용을 담은 메모리의 포인터, 길이, 용량입니다. 이 데이터 그룹은 스택에 저장됩니다. 오른쪽에는 문자열 내용을 담은 힙 메모리가 있습니다.

<img alt="Two tables: the first table contains the representation of s1 on the
stack, consisting of its length (5), capacity (5), and a pointer to the first
value in the second table. The second table contains the representation of the
string data on the heap, byte by byte." src="img/trpl04-01.svg" class="center"
style="width: 50%;" />

<span class="caption">그림 4-1: `"hello"` 값을 가진 `String`이 `s1`에 바인딩된 메모리 표현</span>

길이는 현재 문자열이 사용하는 바이트 수입니다. 용량은 할당자로부터 받은 전체 바이트 수입니다. 길이와 용량의 차이는 지금은 중요하지 않으니, 용량은 무시해도 됩니다.

`s1`을 `s2`에 할당하면, `String` 데이터가 복사됩니다. 즉, 스택에 있는 포인터, 길이, 용량이 복사됩니다. 포인터가 가리키는 힙 데이터는 복사되지 않습니다. 즉, 메모리 표현은 그림 4-2와 같습니다.

<img alt="Three tables: tables s1 and s2 representing those strings on the
stack, respectively, and both pointing to the same string data on the heap."
src="img/trpl04-02.svg" class="center" style="width: 50%;" />

<span class="caption">그림 4-2: `s1`의 포인터, 길이, 용량을 복사한 변수 `s2`의 메모리 표현</span>

이것은 그림 4-3과는 다릅니다. 그림 4-3은 러스트가 힙 데이터까지 복사했다면 메모리가 어떻게 보일지 보여줍니다. 만약 러스트가 이렇게 동작했다면, `s2 = s1` 연산은 힙 데이터가 크면 매우 느려질 수 있습니다.

<img alt="Four tables: two tables representing the stack data for s1 and s2,
and each points to its own copy of string data on the heap."
src="img/trpl04-03.svg" class="center" style="width: 50%;" />

<span class="caption">그림 4-3: 러스트가 힙 데이터까지 복사했다면 `s2 = s1`의 메모리 표현</span>

앞서 변수의 스코프가 끝나면 러스트가 자동으로 `drop`을 호출해 힙 메모리를 정리한다고 했습니다. 하지만 그림 4-2에서는 두 포인터가 같은 위치를 가리킵니다. 이 경우, `s2`와 `s1`이 스코프를 벗어나면 둘 다 같은 메모리를 해제하려고 합니다. 이는 _이중 해제(double free)_ 에러로, 앞서 언급한 메모리 안전 버그 중 하나입니다. 메모리를 두 번 해제하면 메모리 손상과 보안 취약점으로 이어질 수 있습니다.

메모리 안전을 보장하기 위해, `let s2 = s1;` 이후 러스트는 `s1`을 더 이상 유효하지 않은 것으로 간주합니다. 따라서 `s1`이 스코프를 벗어나도 아무것도 해제하지 않습니다. 만약 `s2`를 만든 뒤에 `s1`을 사용하려고 하면, 러스트가 에러를 발생시킵니다:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/src/main.rs:here}}
```

러스트는 무효화된 참조를 사용하지 못하게 막으므로, 아래와 같은 에러가 발생합니다:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/output.txt}}
```

다른 언어에서 _얕은 복사(shallow copy)_와 _깊은 복사(deep copy)_라는 용어를 들어본 적이 있다면, 포인터, 길이, 용량만 복사하고 데이터를 복사하지 않는 것은 얕은 복사와 비슷하다고 느낄 것입니다. 하지만 러스트는 첫 번째 변수를 무효화하므로, 얕은 복사가 아니라 _이동(move)_이라고 부릅니다. 이 예제에서는 `s1`이 `s2`로 _이동_했다고 표현합니다. 실제로 일어나는 일은 그림 4-4와 같습니다.

<img alt="Three tables: tables s1 and s2 representing those strings on the
stack, respectively, and both pointing to the same string data on the heap.
Table s1 is grayed out be-cause s1 is no longer valid; only s2 can be used to
access the heap data." src="img/trpl04-04.svg" class="center" style="width:
50%;" />

<span class="caption">그림 4-4: `s1`이 무효화된 뒤의 메모리 표현</span>

이제 문제는 해결되었습니다! `s2`만 유효하므로, 스코프를 벗어나면 `s2`만 메모리를 해제하면 됩니다.

또한, 러스트는 데이터를 자동으로 "깊게" 복사하지 않습니다. 따라서 _자동_ 복사는 런타임 성능 측면에서 저렴하다고 생각할 수 있습니다.

#### 스코프와 할당

스코프, 소유권, 메모리가 `drop` 함수로 해제되는 관계의 역도 성립합니다. 기존 변수에 완전히 새로운 값을 할당하면, 러스트는 즉시 `drop`을 호출하여 원래 값의 메모리를 해제합니다. 예를 들어:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-04b-replacement-drop/src/main.rs:here}}
```

처음에는 변수 `s`에 `"hello"`라는 값을 가진 `String`을 바인딩합니다. 그 다음, `"ahoy"`라는 새 `String`을 만들어 `s`에 할당합니다. 이 시점에서 원래 힙 값은 아무도 참조하지 않습니다.

<img alt="One table s representing the string value on the stack, pointing to
the second piece of string data (ahoy) on the heap, with the original string
data (hello) grayed out because it cannot be accessed anymore."
src="img/trpl04-05.svg"
class="center"
style="width: 50%;"
/>

<span class="caption">그림 4-5: 원래 값을 완전히 대체한 뒤의 메모리 표현</span>

원래 문자열은 즉시 스코프를 벗어나므로, 러스트가 `drop`을 호출하여 메모리를 바로 해제합니다. 마지막에 출력되는 값은 `"ahoy, world!"`입니다.

<!-- Old heading. Do not remove or links may break. -->

<a id="ways-variables-and-data-interact-clone"></a>

#### 변수와 데이터의 복제(Clone)

만약 `String`의 힙 데이터를 깊게 복사하고 싶다면, 흔히 사용하는 `clone` 메서드를 사용할 수 있습니다. 메서드 문법은 5장에서 다루지만, 많은 언어에서 흔히 사용하므로 익숙할 것입니다.

아래는 `clone` 메서드 사용 예시입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-05-clone/src/main.rs:here}}
```

이 코드는 잘 동작하며, 그림 4-3처럼 힙 데이터까지 복사합니다.

`clone`을 호출하면 임의의 코드가 실행되며, 비용이 많이 들 수 있습니다. `clone`이 보이면, 특별한 동작이 일어난다는 시각적 신호입니다.

#### 스택 전용 데이터: Copy

아직 다루지 않은 부분이 하나 더 있습니다. 정수 예제(리스트 4-2 일부)는 아래처럼 동작하며, 유효한 코드입니다:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-06-copy/src/main.rs:here}}
```

하지만 이 코드는 앞서 배운 내용과 모순되는 것처럼 보입니다. `clone`을 호출하지 않았는데도, `x`는 여전히 유효하고 `y`로 이동되지 않았습니다.

이유는, 정수처럼 크기가 고정된 타입은 스택에만 저장되므로 실제 값 복사가 빠릅니다. 따라서 `y`를 만든 뒤에도 `x`가 유효하지 않게 만들 필요가 없습니다. 즉, 깊은 복사와 얕은 복사 사이에 차이가 없으므로, `clone`을 호출해도 동작이 달라지지 않고, 생략할 수 있습니다.

러스트에는 `Copy` 트레이트라는 특별한 어노테이션이 있습니다. 스택에 저장되는 타입(정수 등)에 이 트레이트를 붙일 수 있습니다(트레이트에 대해서는 [10장][traits]<!-- ignore -->에서 더 다룹니다). `Copy` 트레이트를 구현한 타입은 변수에 할당해도 이동되지 않고, 값이 간단히 복사되어 여전히 유효합니다.

만약 타입이나 그 일부가 `Drop` 트레이트를 구현했다면, 러스트는 `Copy` 어노테이션을 허용하지 않습니다. 값이 스코프를 벗어날 때 특별한 동작이 필요하다면, `Copy`를 붙이면 컴파일 타임 에러가 발생합니다. 타입에 `Copy` 어노테이션을 추가하는 방법은 [부록 C "파생 가능한 트레이트"][derivable-traits]<!-- ignore -->에서 확인하세요.

어떤 타입이 `Copy` 트레이트를 구현하는지 알고 싶다면, 해당 타입의 문서를 확인하세요. 일반적으로, 단순 스칼라 값 집합은 `Copy`를 구현할 수 있고, 할당이 필요하거나 자원 형태인 타입은 `Copy`를 구현할 수 없습니다. 아래는 `Copy`를 구현하는 타입 예시입니다:

- 모든 정수 타입(`u32` 등)
- 불리언 타입(`bool`, 값은 `true`와 `false`)
- 모든 부동소수점 타입(`f64` 등)
- 문자 타입(`char`)
- 튜플(단, 모든 요소가 `Copy`를 구현해야 함. 예: `(i32, i32)`는 `Copy`이지만 `(i32, String)`은 아님)

### 소유권과 함수

값을 함수에 전달하는 동작은 변수에 값을 할당할 때와 비슷합니다. 변수를 함수에 전달하면, 이동 또는 복사가 일어납니다. 리스트 4-3은 함수와 소유권, 스코프를 주석으로 표시한 예시입니다.

<Listing number="4-3" file-name="src/main.rs" caption="함수에서 소유권과 스코프 주석 달기">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-03/src/main.rs}}
```

</Listing>

함수 호출 뒤에 `s`를 사용하려 하면, 러스트는 컴파일 타임 에러를 발생시킵니다. 이런 정적 검사는 실수를 방지해 줍니다. `main`에 `s`와 `x`를 사용하는 코드를 추가해 보면, 어디서 사용할 수 있고, 소유권 규칙 때문에 사용할 수 없는지 알 수 있습니다.

### 반환값과 스코프

값을 반환하는 것도 소유권을 이전할 수 있습니다. 리스트 4-4는 값을 반환하는 함수의 예시입니다.

<Listing number="4-4" file-name="src/main.rs" caption="반환값의 소유권 이전">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-04/src/main.rs}}
```

</Listing>

소유권은 항상 같은 패턴을 따릅니다: 값을 다른 변수에 할당하면 이동됩니다. 힙 데이터를 포함한 변수가 스코프를 벗어나면, 소유권이 다른 변수로 이동하지 않았다면 `drop`으로 정리됩니다.

이 방식은 동작하지만, 함수마다 소유권을 가져갔다가 반환하는 것은 다소 번거롭습니다. 값을 함수에 넘기고 소유권을 가져가지 않게 하려면 어떻게 해야 할까요? 넘긴 값을 다시 받아야 한다면, 함수 본문에서 반환하고 싶은 데이터와 함께 반환해야 하므로 번거롭습니다.

러스트는 튜플을 사용해 여러 값을 반환할 수 있습니다. 리스트 4-5를 참고하세요.

<Listing number="4-5" file-name="src/main.rs" caption="매개변수의 소유권 반환하기">

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-05/src/main.rs}}
```

</Listing>

하지만 이런 방식은 너무 번거롭고, 흔한 개념에 비해 작업이 많습니다. 다행히도, 러스트에는 소유권을 넘기지 않고 값을 사용할 수 있는 _참조_ 기능이 있습니다.

[data-types]: ch03-02-data-types.html#data-types
[ch8]: ch08-02-strings.html
[traits]: ch10-02-traits.html
[derivable-traits]: appendix-03-derivable-traits.html
[method-syntax]: ch05-03-method-syntax.html#method-syntax
[paths-module-tree]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
[drop]: ../std/ops/trait.Drop.html#tymethod.drop