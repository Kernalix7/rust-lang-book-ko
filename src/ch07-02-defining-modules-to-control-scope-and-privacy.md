## 모듈 정의로 범위와 접근 제어하기

이번 섹션에서는 모듈과 모듈 시스템의 다른 부분, 즉 항목에 이름을 붙일 수 있게 해주는 _경로(path)_, 경로를 범위로 가져오는 `use` 키워드, 항목을 공개하는 `pub` 키워드에 대해 다룹니다. 또한 `as` 키워드, 외부 패키지, 글롭(glob) 연산자에 대해서도 설명합니다.

### 모듈 치트시트

모듈과 경로, `use` 키워드, `pub` 키워드가 컴파일러에서 어떻게 동작하는지, 그리고 대부분의 개발자가 코드를 어떻게 구성하는지에 대한 빠른 참고용 요약입니다. 이 장 전체에서 각 규칙의 예시를 다루겠지만, 모듈이 어떻게 동작하는지 기억이 안 날 때 참고하기 좋은 곳입니다.

- **크레이트 루트에서 시작**: 크레이트를 컴파일할 때, 컴파일러는 먼저 크레이트 루트 파일(라이브러리 크레이트는 _src/lib.rs_, 바이너리 크레이트는 _src/main.rs_)에서 코드를 찾습니다.
- **모듈 선언**: 크레이트 루트 파일에서 새 모듈을 선언할 수 있습니다. 예를 들어 `mod garden;`으로 "garden" 모듈을 선언하면, 컴파일러는 아래 위치에서 모듈 코드를 찾습니다:
  - 세미콜론 대신 중괄호로 감싸서 인라인으로 작성한 경우
  - _src/garden.rs_ 파일
  - _src/garden/mod.rs_ 파일
- **하위 모듈 선언**: 크레이트 루트가 아닌 파일에서는 하위 모듈을 선언할 수 있습니다. 예를 들어 _src/garden.rs_에서 `mod vegetables;`를 선언하면, 컴파일러는 부모 모듈 이름의 디렉터리에서 아래 위치를 찾습니다:
  - 세미콜론 대신 중괄호로 인라인 작성한 경우
  - _src/garden/vegetables.rs_ 파일
  - _src/garden/vegetables/mod.rs_ 파일
- **모듈 내 코드 경로**: 모듈이 크레이트에 포함되면, 접근 권한이 허용되는 한 어디서든 해당 모듈의 코드를 경로로 참조할 수 있습니다. 예를 들어, garden 모듈의 vegetables 하위 모듈에 있는 `Asparagus` 타입은 `crate::garden::vegetables::Asparagus` 경로로 찾을 수 있습니다.
- **비공개와 공개**: 모듈 내의 코드는 기본적으로 부모 모듈에서 비공개입니다. 모듈을 공개하려면 `mod` 대신 `pub mod`로 선언하세요. 공개 모듈 내의 항목도 공개하려면 선언 앞에 `pub`을 붙입니다.
- **`use` 키워드**: 범위 내에서 `use` 키워드를 사용하면 긴 경로를 반복하지 않고 항목을 바로 사용할 수 있습니다. 예를 들어, `crate::garden::vegetables::Asparagus`를 참조할 수 있는 범위에서 `use crate::garden::vegetables::Asparagus;`를 선언하면, 이후에는 `Asparagus`만 써도 해당 타입을 사용할 수 있습니다.

아래는 위 규칙을 보여주는 `backyard`라는 바이너리 크레이트의 예시 디렉터리 구조입니다:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

이 경우 크레이트 루트 파일은 _src/main.rs_이며, 그 내용은 다음과 같습니다:

<Listing file-name="src/main.rs">

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/main.rs}}
```

</Listing>

`pub mod garden;` 줄은 컴파일러에게 _src/garden.rs_ 파일의 코드를 포함하라고 지시합니다. 해당 파일의 내용은 다음과 같습니다:

<Listing file-name="src/garden.rs">

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden.rs}}
```

</Listing>

여기서 `pub mod vegetables;`는 _src/garden/vegetables.rs_ 파일의 코드도 포함하라는 의미입니다. 해당 파일의 내용은 다음과 같습니다:

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden/vegetables.rs}}
```

이제 각 규칙의 세부 내용을 실제 예시와 함께 살펴봅시다!

### 관련 코드 묶기: 모듈

_모듈_은 크레이트 내 코드를 읽기 쉽고 재사용하기 쉽게 구성할 수 있게 해줍니다. 또한 모듈을 사용하면 항목의 _비공개성_을 제어할 수 있습니다. 모듈 내의 코드는 기본적으로 비공개이며, 비공개 항목은 외부에서 사용할 수 없는 내부 구현 세부사항입니다. 모듈과 그 내부 항목을 공개로 지정하면 외부 코드에서 사용할 수 있게 됩니다.

예를 들어, 레스토랑 기능을 제공하는 라이브러리 크레이트를 작성한다고 가정해 봅시다. 함수의 시그니처만 정의하고, 구현은 비워두겠습니다. 코드의 조직에 집중하기 위함입니다.

레스토랑 업계에서는 일부 영역을 _front of house_라 하고, 다른 영역을 _back of house_라고 부릅니다. front of house는 손님이 있는 곳으로, 안내원이 손님을 자리로 안내하고, 서버가 주문과 결제를 받고, 바텐더가 음료를 만드는 곳입니다. back of house는 주방에서 셰프와 요리사가 일하고, 식기세척기가 설거지를 하며, 관리자들이 행정 업무를 보는 곳입니다.

이 구조를 코드로 표현하려면, 기능을 중첩된 모듈로 구성할 수 있습니다. `cargo new restaurant --lib`로 `restaurant`라는 새 라이브러리를 만들고, _src/lib.rs_에 아래 리스트 7-1의 코드를 입력하세요. 이 코드는 front of house 부분을 정의합니다.

<Listing number="7-1" file-name="src/lib.rs" caption="다른 모듈을 포함하는 `front_of_house` 모듈과 그 안의 함수들">

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-01/src/lib.rs}}
```

</Listing>

`mod` 키워드와 모듈 이름(`front_of_house`)으로 모듈을 정의합니다. 모듈의 본문은 중괄호 안에 들어갑니다. 모듈 안에는 다른 모듈도 넣을 수 있습니다. 여기서는 `hosting`과 `serving` 모듈이 있습니다. 모듈에는 구조체, 열거형, 상수, 트레이트, 그리고 리스트 7-1처럼 함수도 정의할 수 있습니다.

모듈을 사용하면 관련된 정의를 함께 묶고, 왜 관련되어 있는지 이름을 붙일 수 있습니다. 이 코드를 사용하는 프로그래머는 그룹별로 코드를 탐색할 수 있으므로, 모든 정의를 일일이 읽지 않아도 자신에게 필요한 부분을 쉽게 찾을 수 있습니다. 새로운 기능을 추가하는 프로그래머도 코드를 어디에 추가해야 할지 쉽게 알 수 있습니다.

앞서 _src/main.rs_와 _src/lib.rs_를 크레이트 루트라고 했습니다. 그 이유는 이 두 파일의 내용이 크레이트의 모듈 구조에서 `crate`라는 이름의 모듈을 구성하기 때문입니다. 이를 _모듈 트리_라고 부릅니다.

리스트 7-2는 리스트 7-1의 구조에 대한 모듈 트리를 보여줍니다.

<Listing number="7-2" caption="리스트 7-1 코드의 모듈 트리">

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

</Listing>

이 트리는 일부 모듈이 다른 모듈 안에 중첩되어 있음을 보여줍니다. 예를 들어, `hosting`은 `front_of_house` 안에 중첩되어 있습니다. 또한 일부 모듈은 _형제_ 관계입니다. 즉, 같은 모듈 안에 정의되어 있습니다. `hosting`과 `serving`은 `front_of_house` 안에 정의된 형제 모듈입니다. 모듈 A가 모듈 B 안에 있으면, A는 B의 _자식_이고, B는 A의 _부모_입니다. 전체 모듈 트리는 암묵적으로 `crate`라는 이름의 모듈 아래에 뿌리를 두고 있습니다.

모듈 트리는 컴퓨터의 파일 시스템 디렉터리 트리와 비슷합니다. 실제로 매우 적절한 비유입니다! 파일 시스템의 디렉터리처럼, 모듈을 사용해 코드를 구성합니다. 그리고 디렉터리의 파일처럼, 모듈을 찾는 방법이 필요합니다.