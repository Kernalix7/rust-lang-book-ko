# 숫자 맞추기 게임 프로그래밍하기

이제 러스트를 본격적으로 시작해 봅시다! 이번 장에서는 실제 프로그램을 함께 만들어 보면서 러스트의 몇 가지 주요 개념을 직접 사용해 볼 것입니다. `let`, `match`, 메서드, 연관 함수, 외부 크레이트 등 다양한 러스트 기능을 실습하게 됩니다! 이후 장들에서는 이 개념들을 더 깊이 있게 다루겠지만, 이번 장에서는 기초를 익히는 데 집중합니다.

우리가 구현할 것은 프로그래밍 입문자에게 익숙한 숫자 맞추기 게임입니다. 게임의 동작 방식은 다음과 같습니다: 프로그램이 1부터 100 사이의 임의의 정수를 생성합니다. 플레이어는 숫자를 입력하여 추측합니다. 추측을 입력하면, 프로그램은 그 숫자가 너무 작은지, 너무 큰지 알려줍니다. 만약 정답을 맞추면 축하 메시지를 출력하고 종료합니다.

## 새 프로젝트 준비하기

새 프로젝트를 준비하려면, 1장에서 만든 _projects_ 디렉터리로 이동하여 Cargo로 새 프로젝트를 만드세요:

```console
$ cargo new guessing_game
$ cd guessing_game
```

첫 번째 명령어인 `cargo new`는 프로젝트 이름(`guessing_game`)을 첫 번째 인자로 받습니다. 두 번째 명령어는 새 프로젝트 디렉터리로 이동합니다.

생성된 _Cargo.toml_ 파일을 살펴보세요:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial
rm -rf no-listing-01-cargo-new
cargo new no-listing-01-cargo-new --name guessing_game
cd no-listing-01-cargo-new
cargo run > output.txt 2>&1
cd ../../..
-->

<span class="filename">파일명: Cargo.toml</span>

```toml
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/Cargo.toml}}
```

1장에서 본 것처럼, `cargo new`는 "Hello, world!" 프로그램을 자동으로 생성해 줍니다. _src/main.rs_ 파일을 확인해 보세요:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/src/main.rs}}
```

이제 `cargo run` 명령어로 "Hello, world!" 프로그램을 컴파일하고 실행해 봅시다:

```console
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/output.txt}}
```

`run` 명령어는 빠르게 반복적으로 테스트할 때 유용합니다. 이번 게임에서도 각 단계마다 빠르게 실행해 볼 것입니다.

_src/main.rs_ 파일을 다시 열어주세요. 앞으로 모든 코드는 이 파일에 작성합니다.

## 추측값 처리하기

게임의 첫 번째 단계는 사용자 입력을 받아서, 그 입력을 처리하고, 입력이 예상한 형태인지 확인하는 것입니다. 먼저 플레이어가 추측값을 입력할 수 있도록 해봅시다. 아래 코드(리스트 2-1)를 _src/main.rs_에 입력하세요.

<Listing number="2-1" file-name="src/main.rs" caption="사용자로부터 추측값을 받아 출력하는 코드">

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:all}}
```

</Listing>

이 코드는 많은 정보를 담고 있으니, 한 줄씩 살펴보겠습니다. 사용자 입력을 받고 결과를 출력하려면, `io` 입출력 라이브러리를 스코프로 가져와야 합니다. `io` 라이브러리는 표준 라이브러리인 `std`에서 가져옵니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:io}}
```

러스트는 기본적으로 표준 라이브러리에 정의된 일부 항목을 모든 프로그램의 스코프로 가져옵니다. 이 집합을 _프렐류드_라고 하며, [표준 라이브러리 문서][prelude]에서 전체 목록을 볼 수 있습니다.

사용하고 싶은 타입이 프렐류드에 없다면, 반드시 `use` 문으로 직접 스코프로 가져와야 합니다. `std::io` 라이브러리를 사용하면 사용자 입력을 받을 수 있는 등 여러 유용한 기능을 제공합니다.

1장에서 본 것처럼, `main` 함수는 프로그램의 진입점입니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:main}}
```

`fn` 문법은 새 함수를 선언하며, 괄호 `()`는 매개변수가 없음을, 중괄호 `{`는 함수 본문의 시작을 나타냅니다.

또한 1장에서 배운 대로, `println!`은 문자열을 화면에 출력하는 매크로입니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:print}}
```

이 코드는 게임의 안내 메시지와 사용자 입력 요청을 출력합니다.

### 변수로 값 저장하기

다음으로, 사용자 입력을 저장할 _변수_를 만들어 봅시다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:string}}
```

이제 프로그램이 점점 흥미로워집니다! 이 한 줄에는 많은 내용이 담겨 있습니다. `let` 문으로 변수를 생성합니다. 예를 들어:

```rust,ignore
let apples = 5;
```

이 줄은 `apples`라는 새 변수를 만들고 값 5를 바인딩합니다. 러스트에서 변수는 기본적으로 불변(immutable)입니다. 즉, 변수에 값을 할당하면 그 값은 변경되지 않습니다. 이 개념은 [3장 "변수와 가변성"][variables-and-mutability]<!-- ignore -->에서 자세히 다룹니다. 변수를 가변(mutable)하게 만들려면 변수 이름 앞에 `mut`를 붙입니다:

```rust,ignore
let apples = 5; // 불변
let mut bananas = 5; // 가변
```

> 참고: `//` 문법은 줄 끝까지 이어지는 주석을 시작합니다. 러스트는 주석의 모든 내용을 무시합니다. 주석에 대해서는 [3장][comments]<!-- ignore -->에서 더 자세히 다룹니다.

숫자 맞추기 게임으로 돌아가면, `let mut guess`는 `guess`라는 가변 변수를 도입합니다. 등호(`=`)는 변수에 값을 바인딩하겠다는 의미입니다. 등호 오른쪽에는 `guess`에 바인딩할 값이 있는데, 이는 `String::new`를 호출한 결과입니다. `String`은 표준 라이브러리에서 제공하는 타입으로, 확장 가능한 UTF-8 인코딩 텍스트입니다.

`::` 문법은 `new`가 `String` 타입의 연관 함수임을 나타냅니다. _연관 함수_란 타입에 구현된 함수로, 여기서는 `String`에 구현된 함수입니다. 이 `new` 함수는 새 빈 문자열을 만듭니다. 많은 타입에서 `new` 함수가 있는데, 이는 새로운 값을 만드는 함수의 흔한 이름이기 때문입니다.

정리하면, `let mut guess = String::new();`는 새 빈 `String` 인스턴스를 가변 변수에 바인딩한 것입니다.

### 사용자 입력 받기

프로그램 첫 줄에서 `use std::io;`로 입출력 기능을 가져왔던 것을 기억하세요. 이제 `io` 모듈의 `stdin` 함수를 호출하여 사용자 입력을 처리합니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:read}}
```

만약 프로그램 시작 부분에서 `use std::io;`를 하지 않았다면, `std::io::stdin`으로도 함수를 사용할 수 있습니다. `stdin` 함수는 [`std::io::Stdin`][iostdin]<!-- ignore --> 타입의 인스턴스를 반환하는데, 이는 터미널의 표준 입력을 나타냅니다.

다음 줄인 `.read_line(&mut guess)`는 표준 입력 핸들에서 [`read_line`][read_line]<!-- ignore --> 메서드를 호출하여 사용자 입력을 받습니다. `read_line`의 인자로 `&mut guess`를 넘겨서 입력값을 저장할 문자열을 지정합니다. `read_line`의 역할은 사용자가 입력한 내용을 문자열에 추가하는 것이므로, 해당 문자열을 인자로 넘깁니다. 문자열 인자는 내용이 변경되어야 하므로 가변이어야 합니다.

`&`는 _참조_를 나타내며, 여러 코드 부분이 하나의 데이터를 복사하지 않고 접근할 수 있게 해줍니다. 참조는 러스트의 주요 기능 중 하나이며, 안전하고 쉽게 사용할 수 있다는 점이 러스트의 큰 장점입니다. 지금은 참조의 세부 내용을 몰라도 괜찮습니다. 변수와 마찬가지로 참조도 기본적으로 불변입니다. 따라서 가변 참조를 위해 `&mut guess`라고 작성해야 합니다. (참조에 대해서는 4장에서 더 자세히 설명합니다.)

<!-- Old heading. Do not remove or links may break. -->

<a id="handling-potential-failure-with-the-result-type"></a>

### `Result`로 발생 가능한 실패 처리하기

아직 이 코드 줄을 다 살펴보지 않았습니다. 세 번째 줄을 살펴보면, 여전히 하나의 논리적 코드 줄의 일부입니다. 다음은 이 메서드입니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:expect}}
```

이 코드는 다음과 같이 한 줄로도 쓸 수 있습니다:

```rust,ignore
io::stdin().read_line(&mut guess).expect("Failed to read line");
```

하지만 한 줄이 너무 길면 읽기 어렵기 때문에, 메서드를 `.method_name()` 문법으로 호출할 때는 줄바꿈과 공백을 적절히 사용하는 것이 좋습니다. 이제 이 줄이 무엇을 하는지 살펴봅시다.

앞서 언급했듯이, `read_line`은 사용자가 입력한 내용을 우리가 넘긴 문자열에 넣어주지만, 동시에 `Result` 값을 반환합니다. [`Result`][result]<!-- ignore -->는 [_열거형_][enums]<!-- ignore -->(enum)으로, 여러 가능한 상태 중 하나를 가질 수 있는 타입입니다. 각 상태를 _변형(variant)_이라고 부릅니다.

[6장][enums]<!-- ignore -->에서 열거형에 대해 더 자세히 다룹니다. `Result` 타입의 목적은 에러 처리 정보를 담는 것입니다.

`Result`의 변형은 `Ok`와 `Err`입니다. `Ok`는 작업이 성공했음을 나타내며, 성공적으로 생성된 값을 포함합니다. `Err`는 작업이 실패했음을 의미하며, 실패의 원인이나 정보를 담고 있습니다.

`Result` 타입의 값에는 다양한 메서드가 정의되어 있습니다. `Result` 인스턴스에는 [`expect` 메서드][expect]<!-- ignore -->가 있습니다. 만약 이 `Result`가 `Err` 값이라면, `expect`는 프로그램을 크래시시키고 인자로 넘긴 메시지를 출력합니다. 만약 `read_line`이 `Err`를 반환한다면, 이는 운영체제에서 발생한 에러 때문일 것입니다. 만약 `Result`가 `Ok` 값이라면, `expect`는 `Ok`가 담고 있는 값을 반환하여 사용할 수 있게 해줍니다. 여기서는 사용자의 입력 바이트 수가 그 값입니다.

`expect`를 호출하지 않으면 프로그램은 컴파일되지만, 경고가 발생합니다:

```console
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-02-without-expect/output.txt}}
```

러스트는 `read_line`이 반환한 `Result` 값을 사용하지 않았다고 경고합니다. 즉, 프로그램이 발생 가능한 에러를 처리하지 않았다는 뜻입니다.

경고를 없애는 올바른 방법은 실제로 에러 처리 코드를 작성하는 것이지만, 여기서는 문제가 발생하면 프로그램을 크래시시키고 싶으므로 `expect`를 사용합니다. 에러 복구에 대해서는 [9장][recover]<!-- ignore -->에서 배웁니다.

### `println!` 플레이스홀더로 값 출력하기

마지막 중괄호를 제외하면, 지금까지의 코드에서 논의할 줄이 하나 더 남았습니다:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:print_guess}}
```

이 줄은 사용자의 입력값이 담긴 문자열을 출력합니다. `{}` 중괄호는 플레이스홀더입니다: `{}`는 값을 그 자리에 끼워 넣는 집게발이라고 생각하면 됩니다. 변수 값을 출력할 때는 변수 이름을 중괄호 안에 넣을 수 있습니다. 표현식의 결과를 출력할 때는 포맷 문자열에 빈 중괄호를 두고, 그 뒤에 쉼표로 구분된 표현식 목록을 넣으면 각 중괄호에 순서대로 값이 들어갑니다. 변수와 표현식 결과를 한 번에 출력하려면 다음과 같이 합니다:

```rust
let x = 5;
let y = 10;

println!("x = {x} and y + 2 = {}", y + 2);
```

이 코드는 `x = 5 and y + 2 = 12`를 출력합니다.

### 첫 번째 부분 테스트하기

이제 숫자 맞추기 게임의 첫 번째 부분을 테스트해 봅시다. `cargo run`으로 실행하세요:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-01/
cargo clean
cargo run
input 6 -->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 6.44s
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

이 시점에서 게임의 첫 번째 부분이 완성되었습니다: 키보드에서 입력을 받고, 그 값을 출력합니다.

## 비밀 숫자 생성하기

이제 사용자가 맞춰야 할 비밀 숫자를 생성해야 합니다. 비밀 숫자는 매번 달라야 게임이 여러 번 해도 재미있습니다. 1부터 100 사이의 임의의 숫자를 사용하면 게임이 너무 어렵지 않게 됩니다. 러스트 표준 라이브러리에는 아직 난수 생성 기능이 포함되어 있지 않습니다. 하지만 러스트 팀이 [`rand` 크레이트][randcrate]를 제공하고 있습니다.

### 크레이트로 기능 확장하기

크레이트란 러스트 소스 코드 파일들의 모음입니다. 우리가 만든 프로젝트는 _바이너리 크레이트_로, 실행 파일입니다. `rand` 크레이트는 _라이브러리 크레이트_로, 다른 프로그램에서 사용하도록 만들어졌으며 단독 실행은 불가능합니다.

Cargo가 외부 크레이트를 관리하는 부분이 바로 Cargo의 진가가 드러나는 곳입니다. `rand`를 사용하려면 먼저 _Cargo.toml_ 파일을 수정하여 `rand` 크레이트를 의존성으로 추가해야 합니다. 해당 파일을 열고, `[dependencies]` 섹션 아래에 다음 줄을 추가하세요. 반드시 아래와 같이 버전까지 정확히 입력해야 예제 코드가 정상 동작합니다:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
* ch14-03-cargo-workspaces.md
-->

<span class="filename">파일명: Cargo.toml</span>

```toml
{{#include ../listings/ch02-guessing-game-tutorial/listing-02-02/Cargo.toml:8:}}
```

_Cargo.toml_ 파일에서 헤더 다음에 오는 모든 내용은 해당 섹션에 속합니다. `[dependencies]`에는 프로젝트가 의존하는 외부 크레이트와 필요한 버전을 지정합니다. 여기서는 `rand` 크레이트를 의미론적 버전 지정자인 `0.8.5`로 명시합니다. Cargo는 [Semantic Versioning][semver]<!-- ignore -->을 이해합니다. `0.8.5`는 사실상 `^0.8.5`의 축약형으로, 0.8.5 이상 0.9.0 미만의 모든 버전을 의미합니다.

Cargo는 이 버전들이 0.8.5와 호환되는 공개 API를 가진 것으로 간주하며, 이 지정 덕분에 이 장의 코드와 호환되는 최신 패치 릴리스를 사용할 수 있습니다. 0.9.0 이상의 버전은 예제 코드와 동일한 API를 보장하지 않습니다.

이제 코드를 변경하지 않고 프로젝트를 빌드해 봅시다(리스트 2-2).

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
rm Cargo.lock
cargo clean
cargo build -->

<Listing number="2-2" caption="rand 크레이트를 의존성으로 추가한 뒤 `cargo build` 실행 결과">

```console
$ cargo build
  Updating crates.io index
   Locking 15 packages to latest Rust 1.85.0 compatible versions
    Adding rand v0.8.5 (available: v0.9.0)
 Compiling proc-macro2 v1.0.93
 Compiling unicode-ident v1.0.17
 Compiling libc v0.2.170
 Compiling cfg-if v1.0.0
 Compiling byteorder v1.5.0
 Compiling getrandom v0.2.15
 Compiling rand_core v0.6.4
 Compiling quote v1.0.38
 Compiling syn v2.0.98
 Compiling zerocopy-derive v0.7.35
 Compiling zerocopy v0.7.35
 Compiling ppv-lite86 v0.2.20
 Compiling rand_chacha v0.3.1
 Compiling rand v0.8.5
 Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
  Finished `dev` profile [unoptimized + debuginfo] target(s) in 2.48s
```

</Listing>

버전 번호나 출력 줄이 다를 수 있지만, 모두 코드와 호환됩니다(SemVer 덕분입니다!). 운영체제에 따라 줄의 순서도 달라질 수 있습니다.

외부 의존성을 추가하면, Cargo는 해당 의존성이 필요로 하는 최신 버전들을 _레지스트리_에서 가져옵니다. 레지스트리는 [Crates.io][cratesio]의 데이터를 복사한 것입니다. Crates.io는 러스트 생태계의 오픈 소스 프로젝트들이 모여 있는 곳입니다.

레지스트리를 업데이트한 후, Cargo는 `[dependencies]` 섹션을 확인하여 아직 다운로드하지 않은 크레이트를 다운로드합니다. 여기서는 `rand`만 명시했지만, Cargo는 `rand`가 필요로 하는 다른 크레이트들도 함께 가져옵니다. 크레이트를 다운로드한 뒤, 러스트는 이들을 컴파일하고, 의존성이 준비된 상태에서 프로젝트를 컴파일합니다.

바로 `cargo build`를 다시 실행하면, `Finished` 줄만 출력됩니다. Cargo는 이미 의존성을 다운로드하고 컴파일했으며, 코드도 변경되지 않았으므로 다시 컴파일하지 않습니다. 할 일이 없으니 바로 종료합니다.

_src/main.rs_ 파일을 열어 사소한 변경을 한 뒤 저장하고 빌드하면, 두 줄만 출력됩니다:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
touch src/main.rs
cargo build -->

```console
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.13s
```

이 줄들은 Cargo가 _src/main.rs_ 파일의 작은 변경만 빌드에 반영했음을 보여줍니다. 의존성은 변경되지 않았으므로, 이미 다운로드하고 컴파일한 것을 재사용합니다.

#### _Cargo.lock_ 파일로 빌드 재현성 보장하기

Cargo에는 항상 동일한 결과물을 빌드할 수 있도록 해주는 메커니즘이 있습니다. 여러분이나 다른 사람이 코드를 빌드할 때마다, Cargo는 지정한 버전만 사용합니다. 예를 들어, 다음 주에 `rand` 크레이트의 0.8.6 버전이 출시되어 중요한 버그가 수정되었지만, 동시에 여러분의 코드를 깨뜨릴 수 있는 회귀가 포함되어 있다고 가정해 봅시다. 이를 처리하기 위해 러스트는 처음 `cargo build`를 실행할 때 _Cargo.lock_ 파일을 생성합니다. 이제 _guessing_game_ 디렉터리에 이 파일이 생겼습니다.

프로젝트를 처음 빌드할 때, Cargo는 모든 의존성의 버전을 결정하여 _Cargo.lock_ 파일에 기록합니다. 이후 프로젝트를 빌드하면, Cargo는 _Cargo.lock_ 파일을 확인하여 거기에 명시된 버전만 사용합니다. 덕분에 자동으로 빌드 재현성이 보장됩니다. 즉, _Cargo.lock_ 파일 덕분에 프로젝트는 명시적으로 업그레이드하지 않는 한 0.8.5 버전을 계속 사용합니다. _Cargo.lock_ 파일은 빌드 재현성에 중요하므로, 프로젝트의 다른 코드와 함께 소스 관리에 포함하는 경우가 많습니다.

#### 크레이트 버전 업데이트하기

크레이트를 업데이트하고 싶을 때는, Cargo의 `update` 명령어를 사용하면 _Cargo.lock_ 파일을 무시하고 _Cargo.toml_의 지정에 맞는 최신 버전을 모두 결정합니다. 그리고 그 버전들을 _Cargo.lock_ 파일에 기록합니다. 이 경우, Cargo는 0.8.5보다 크고 0.9.0보다 작은 버전만 찾습니다. 만약 `rand` 크레이트가 0.8.6과 0.9.0 두 버전을 출시했다면, `cargo update`를 실행하면 다음과 같이 나옵니다:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
cargo update
assuming there is a new 0.8.x version of rand; otherwise use another update
as a guide to creating the hypothetical output shown here -->

```console
$ cargo update
    Updating crates.io index
     Locking 1 package to latest Rust 1.85.0 compatible version
    Updating rand v0.8.5 -> v0.8.6 (available: v0.9.0)
```

Cargo는 0.9.0 릴리스를 무시합니다. 이 시점에서 _Cargo.lock_ 파일에는 이제 `rand` 크레이트의 버전이 0.8.6으로 바뀐 것이 표시됩니다. 만약 0.9.0이나 0.9.x 버전을 사용하고 싶다면, _Cargo.toml_ 파일을 다음과 같이 수정해야 합니다:

```toml
[dependencies]
rand = "0.9.0"
```

다음 번에 `cargo build`를 실행하면, Cargo는 사용 가능한 크레이트 레지스트리를 업데이트하고, 새 버전에 맞게 `rand` 요구사항을 다시 평가합니다.

Cargo와 [그 생태계][doccargo]<!-- ignore -->, [Crates.io][doccratesio]<!-- ignore -->에 대해 더 할 말이 많지만, 14장에서 자세히 다루겠습니다. 지금은 이 정도만 알아두면 충분합니다. Cargo 덕분에 라이브러리를 쉽게 재사용할 수 있으므로, 러스타시안들은 여러 패키지로 구성된 작은 프로젝트를 쉽게 만들 수 있습니다.

### 난수 생성하기

이제 `rand`를 사용해 맞출 숫자를 생성해 봅시다. 다음 단계는 _src/main.rs_를 아래와 같이 수정하는 것입니다(리스트 2-3).

<Listing number="2-3" file-name="src/main.rs" caption="난수 생성 코드 추가하기">

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-03/src/main.rs:all}}
```

</Listing>

먼저 `use rand::Rng;` 줄을 추가합니다. `Rng` 트레이트는 난수 생성기가 구현해야 하는 메서드를 정의하며, 해당 메서드를 사용하려면 반드시 스코프로 가져와야 합니다. 트레이트에 대해서는 10장에서 자세히 다룹니다.

다음으로 중간에 두 줄을 추가합니다. 첫 번째 줄에서는 `rand::thread_rng` 함수를 호출하여 사용할 난수 생성기를 얻습니다. 이 생성기는 현재 실행 중인 스레드에 로컬이며, 운영체제에서 시드를 받습니다. 그 다음, 난수 생성기에서 `gen_range` 메서드를 호출합니다. 이 메서드는 `Rng` 트레이트에 정의되어 있으며, `use rand::Rng;`로 스코프로 가져왔기 때문에 사용할 수 있습니다. `gen_range`는 범위 표현식을 인자로 받아, 해당 범위 내의 난수를 생성합니다. 여기서 사용하는 범위 표현식은 `start..=end` 형태로, 하한과 상한 모두 포함합니다. 따라서 1부터 100까지의 숫자를 얻으려면 `1..=100`을 지정해야 합니다.

> 참고: 어떤 트레이트를 사용해야 하고, 크레이트에서 어떤 메서드와 함수를 호출해야 하는지는 직접 알기 어렵습니다. 그래서 각 크레이트에는 사용법을 안내하는 문서가 있습니다. Cargo의 또 다른 멋진 기능은 `cargo doc --open` 명령어를 실행하면 모든 의존성의 문서를 로컬에서 빌드하고 브라우저로 열어준다는 점입니다. 예를 들어 `rand` 크레이트의 다른 기능이 궁금하다면, `cargo doc --open`을 실행하고 왼쪽 사이드바에서 `rand`를 클릭해 보세요.

두 번째 새 줄은 비밀 숫자를 출력합니다. 개발 중에는 테스트를 위해 유용하지만, 최종 버전에서는 삭제할 것입니다. 게임 시작과 동시에 정답을 출력하면 게임이 성립하지 않으니까요!

프로그램을 여러 번 실행해 보세요:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-03/
cargo run
4
cargo run
5
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.02s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4

$ cargo run
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.02s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

매번 다른 난수가 나오고, 모두 1부터 100 사이의 숫자여야 합니다. 훌륭합니다!

## 추측값과 비밀 숫자 비교하기

이제 사용자 입력과 난수를 비교할 수 있습니다. 그 단계는 리스트 2-4에 나와 있습니다. 참고로 이 코드는 아직 컴파일되지 않습니다. 그 이유는 아래에서 설명합니다.

<Listing number="2-4" file-name="src/main.rs" caption="두 숫자를 비교한 결과값 처리하기">

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-04/src/main.rs:here}}
```

</Listing>

먼저 또 하나의 `use` 문을 추가하여 표준 라이브러리에서 `std::cmp::Ordering` 타입을 스코프로 가져옵니다. `Ordering` 타입은 또 다른 열거형이며, 변형으로 `Less`, `Greater`, `Equal`이 있습니다. 두 값을 비교할 때 가능한 세 가지 결과입니다.

그 다음, 아래에 다섯 줄을 추가하여 `Ordering` 타입을 사용합니다. `cmp` 메서드는 두 값을 비교하며, 비교 가능한 모든 것에 사용할 수 있습니다. 비교할 값을 참조로 넘깁니다: 여기서는 `guess`와 `secret_number`를 비교합니다. 그리고 `Ordering` 열거형의 변형 중 하나를 반환합니다. 우리는 [`match`][match]<!-- ignore --> 표현식을 사용하여, `cmp`가 반환한 변형에 따라 다음에 할 일을 결정합니다.

`match` 표현식은 _팔(arm)_로 구성됩니다. 팔은 매칭할 _패턴_과, 값이 해당 패턴에 맞을 때 실행할 코드로 이루어집니다. 러스트는 `match`에 넘긴 값을 각 팔의 패턴과 차례로 비교합니다. 패턴과 `match`는 러스트의 강력한 기능으로, 다양한 상황을 표현하고 모든 경우를 처리하도록 강제합니다. 이 기능들은 6장과 19장에서 자세히 다룹니다.

여기서 사용한 `match` 예제를 살펴봅시다. 사용자가 50을 입력했고, 비밀 숫자가 38이라고 가정합니다.

코드가 50과 38을 비교하면, `cmp` 메서드는 50이 38보다 크므로 `Ordering::Greater`를 반환합니다. `match` 표현식은 `Ordering::Greater` 값을 받아 각 팔의 패턴을 확인합니다. 첫 번째 팔의 패턴은 `Ordering::Less`인데, 값이 맞지 않으므로 건너뜁니다. 다음 팔의 패턴은 `Ordering::Greater`로, 값이 일치하므로 해당 코드를 실행하여 `Too big!`을 출력합니다. `match`는 첫 번째로 일치하는 팔에서 실행을 멈춥니다.

하지만 리스트 2-4의 코드는 아직 컴파일되지 않습니다. 실행해 보면 다음과 같은 에러가 발생합니다:

<!--
The error numbers in this output should be that of the code **WITHOUT** the
anchor or snip comments
-->

```console
{{#include ../listings/ch02-guessing-game-tutorial/listing-02-04/output.txt}}
```

핵심 에러는 _타입 불일치_입니다. 러스트는 강력한 정적 타입 시스템을 가지고 있지만, 타입 추론도 지원합니다. `let mut guess = String::new()`를 작성하면, 러스트는 `guess`가 `String` 타입임을 추론합니다. 반면, `secret_number`는 숫자 타입입니다. 러스트의 숫자 타입 중 1부터 100까지 값을 가질 수 있는 것은 `i32`, `u32`, `i64` 등이 있습니다. 별도로 지정하지 않으면 러스트는 기본적으로 `i32`를 사용합니다. 즉, `secret_number`는 타입 정보가 없으면 `i32`입니다. 에러의 원인은 문자열과 숫자 타입을 비교할 수 없기 때문입니다.

궁극적으로, 입력받은 `String`을 숫자 타입으로 변환해야 비밀 숫자와 비교할 수 있습니다. 이를 위해 `main` 함수 본문에 다음 줄을 추가합니다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-03-convert-string-to-number/src/main.rs:here}}
```

해당 줄은 다음과 같습니다:

```rust,ignore
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

`guess`라는 변수를 새로 만듭니다. 그런데 이미 `guess`라는 변수가 있지 않나요? 맞습니다. 하지만 러스트는 이전 값을 새 값으로 _섀도잉(shadowing)_할 수 있게 해줍니다. 섀도잉을 사용하면 변수 이름을 재사용할 수 있어, 예를 들어 `guess_str`과 `guess`처럼 두 개의 변수를 만들 필요가 없습니다. 섀도잉은 값을 한 타입에서 다른 타입으로 변환할 때 자주 사용합니다. [3장][shadowing]<!-- ignore -->에서 더 자세히 다룹니다.

이 새 변수는 `guess.trim().parse()` 표현식에 바인딩됩니다. 여기서 `guess`는 원래 입력값을 담고 있던 변수입니다. `trim` 메서드는 문자열 앞뒤의 공백을 제거합니다. 숫자로 변환하기 전에 반드시 공백을 없애야 합니다. 사용자가 <kbd>enter</kbd>를 눌러 입력을 완료하면, 문자열에 줄바꿈 문자가 추가됩니다. 예를 들어 <kbd>5</kbd>를 입력하고 <kbd>enter</kbd>를 누르면, `guess`는 `5\n`이 됩니다. `\n`은 줄바꿈을 의미합니다(Windows에서는 `\r\n`). `trim`은 이를 제거하여 `5`만 남깁니다.

문자열의 [`parse` 메서드][parse]<!-- ignore -->는 문자열을 다른 타입으로 변환합니다. 여기서는 문자열을 숫자로 변환합니다. 어떤 숫자 타입으로 변환할지 러스트에 알려주기 위해 `let guess: u32`로 타입을 명시합니다. 콜론(`:`) 뒤에 타입을 지정하면 됩니다. `u32`는 부호 없는 32비트 정수로, 작은 양의 정수에 적합합니다. 다른 숫자 타입에 대해서는 [3장][integers]<!-- ignore -->에서 배웁니다.

또한, 이 예제에서 `u32`로 타입을 지정하고 `secret_number`와 비교하므로, 러스트는 `secret_number`도 `u32`로 추론합니다. 이제 두 값의 타입이 같아졌습니다!

`parse` 메서드는 논리적으로 숫자로 변환할 수 있는 문자만 처리할 수 있으므로, 에러가 쉽게 발생할 수 있습니다. 예를 들어, 문자열에 `A👍%`가 들어 있다면 숫자로 변환할 수 없습니다. 실패할 수 있기 때문에, `parse`는 `Result` 타입을 반환합니다. 앞서 [“`Result`로 발생 가능한 실패 처리하기”](#handling-potential-failure-with-result)<!-- ignore-->에서 설명한 것처럼, 여기서도 `expect`를 사용합니다. 만약 `parse`가 변환에 실패하면, `expect`가 게임을 크래시시키고 지정한 메시지를 출력합니다. 변환에 성공하면, `Ok` 변형의 값을 반환하여 우리가 원하는 숫자를 얻을 수 있습니다.

이제 프로그램을 실행해 봅시다:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-03-convert-string-to-number/
touch src/main.rs
cargo run
  76
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.26s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

공백을 넣어도 프로그램이 76을 입력한 것으로 인식합니다. 여러 번 실행해 보면서, 정답을 맞추거나 너무 크거나 너무 작은 값을 입력해 보세요.

이제 게임의 대부분이 동작하지만, 사용자는 한 번만 추측할 수 있습니다. 반복해서 추측할 수 있도록 루프를 추가해 봅시다!

## 루프로 여러 번 추측 허용하기

`loop` 키워드는 무한 루프를 만듭니다. 루프를 추가하여 사용자가 여러 번 추측할 수 있게 해봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-04-looping/src/main.rs:here}}
```

보시다시피, 입력 프롬프트 이후 모든 코드를 루프 안으로 옮겼습니다. 루프 내부 코드는 네 칸씩 들여써야 하며, 프로그램을 다시 실행해 보세요. 이제 프로그램은 무한히 추측을 요청합니다. 하지만 새로운 문제가 생겼습니다. 사용자가 종료할 수 없어 보입니다!

사용자는 <kbd>ctrl</kbd>-<kbd>c</kbd>로 프로그램을 강제 종료할 수 있습니다. 하지만 [“추측값과 비밀 숫자 비교하기”](#comparing-the-guess-to-the-secret-number)<!-- ignore -->에서 설명한 대로, 숫자가 아닌 값을 입력하면 프로그램이 크래시합니다. 이를 이용해 사용자가 종료할 수 있습니다:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-04-looping/
touch src/main.rs
cargo run
(too small guess)
(too big guess)
(correct guess)
quit
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.23s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit

thread 'main' panicked at src/main.rs:28:47:
Please type a number!: ParseIntError { kind: InvalidDigit }
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

`quit`을 입력하면 게임이 종료되지만, 숫자가 아닌 아무 값이나 입력해도 종료됩니다. 이는 바람직하지 않습니다. 정답을 맞췄을 때도 게임이 종료되어야 합니다.

### 정답을 맞추면 종료하기

사용자가 정답을 맞추면 게임이 종료되도록 `break` 문을 추가해 봅시다:

<span class="filename">파일명: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-05-quitting/src/main.rs:here}}
```

`You win!` 이후에 `break` 줄을 추가하면, 사용자가 정답을 맞췄을 때 루프를 빠져나가고, 프로그램도 종료됩니다. 루프가 `main`의 마지막 부분이기 때문입니다.

### 잘못된 입력 처리하기

게임의 동작을 더 다듬기 위해, 사용자가 숫자가 아닌 값을 입력했을 때 프로그램이 크래시하지 않고 무시하도록 만들어 봅시다. 이를 위해 `guess`를 `String`에서 `u32`로 변환하는 줄을 아래와 같이 수정합니다(리스트 2-5).

<Listing number="2-5" file-name="src/main.rs" caption="숫자가 아닌 추측값을 무시하고 다시 입력받기">

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-05/src/main.rs:here}}
```

</Listing>

`expect` 대신 `match` 표현식을 사용하여 에러 발생 시 크래시하지 않고 처리합니다. `parse`는 `Result` 타입을 반환하며, `Result`는 `Ok`와 `Err` 변형을 가집니다. 여기서도 `cmp` 메서드의 결과를 처리할 때처럼 `match`를 사용합니다.

`parse`가 문자열을 성공적으로 숫자로 변환하면, `Ok` 값에 결과 숫자가 들어 있습니다. 이 값은 첫 번째 팔의 패턴과 일치하므로, `match`는 `num` 값을 반환하여 새 `guess` 변수에 할당합니다.

`parse`가 변환에 실패하면, `Err` 값에 에러 정보가 들어 있습니다. `Err` 값은 첫 번째 팔의 패턴과 일치하지 않지만, 두 번째 팔의 `Err(_)` 패턴과 일치합니다. 언더스코어 `_`는 모든 값을 포괄하는 패턴입니다. 즉, 모든 `Err` 값에 대해 두 번째 팔의 코드를 실행합니다. 여기서는 `continue`를 실행하여 루프의 다음 반복으로 넘어갑니다. 결과적으로, `parse`에서 발생하는 모든 에러를 무시하게 됩니다!

이제 프로그램이 정상적으로 동작해야 합니다. 실행해 봅시다:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-05/
cargo run
(too small guess)
(too big guess)
foo
(correct guess)
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.13s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

훌륭합니다! 마지막으로 아주 작은 수정을 하면 게임이 완성됩니다. 프로그램이 아직 비밀 숫자를 출력하고 있는데, 테스트에는 좋지만 게임을 망칩니다. 비밀 숫자를 출력하는 `println!`을 삭제하면 완성입니다. 리스트 2-6은 최종 코드입니다.

<Listing number="2-6" file-name="src/main.rs" caption="완성된 숫자 맞추기 게임 코드">

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-06/src/main.rs}}
```

</Listing>

이제 숫자 맞추기 게임을 성공적으로 완성했습니다. 축하합니다!

## 요약

이번 프로젝트를 통해 `let`, `match`, 함수, 외부 크레이트 사용 등 러스트의 다양한 새 개념을 직접 익혔습니다. 다음 장들에서는 이 개념들을 더 자세히 배웁니다. 3장에서는 대부분의 프로그래밍 언어가 가진 변수, 데이터 타입, 함수 등 기본 개념을 러스트에서 어떻게 사용하는지 다룹니다. 4장에서는 러스트만의 특징인 소유권을 탐구합니다. 5장에서는 구조체와 메서드 문법을, 6장에서는 열거형의 동작 방식을 설명합니다.

[prelude]: ../std/prelude/index.html
[variables-and-mutability]: ch03-01-variables-and-mutability.html#variables-and-mutability
[comments]: ch03-04-comments.html
[string]: ../std/string/struct.String.html
[iostdin]: ../std/io/struct.Stdin.html
[read_line]: ../std/io/struct.Stdin.html#method.read_line
[result]: ../std/result/enum.Result.html
[enums]: ch06-00-enums.html
[expect]: ../std/result/enum.Result.html#method.expect
[recover]: ch09-02-recoverable-errors-with-result.html
[randcrate]: https://crates.io/crates/rand
[semver]: http://semver.org
[cratesio]: https://crates.io/
[doccargo]: https://doc.rust-lang.org/cargo/
[doccratesio]: https://doc.rust-lang.org/cargo/reference/publishing.html
[match]: ch06-02-match.html
[shadowing]: ch03-01-variables-and-mutability.html#shadowing
[parse]: ../std/primitive.str.html#method.parse
[integers]: ch03-02-data-types.html#integer-types
the user has guessed 50 and the randomly generated secret number this time is
38.