## Hello, Cargo!

Cargo는 러스트의 빌드 시스템이자 패키지 매니저입니다. 대부분의 러스타시안들은 이 도구를 사용하여 러스트 프로젝트를 관리합니다. 이는 Cargo가 코드 빌드, 코드가 의존하는 라이브러리 다운로드, 그리고 그 라이브러리 빌드와 같은 많은 작업을 대신 처리해 주기 때문입니다. (코드가 필요로 하는 라이브러리를 _의존성_이라고 부릅니다.)

지금까지 작성한 것과 같은 가장 단순한 러스트 프로그램은 의존성이 없습니다. 만약 "Hello, world!" 프로젝트를 Cargo로 빌드했다면, 코드를 빌드하는 Cargo 부분만 사용하게 됩니다. 더 복잡한 러스트 프로그램을 작성할 때는 의존성을 추가하게 될 것입니다. Cargo로 프로젝트를 시작하면 의존성 추가가 훨씬 쉬워집니다.

대다수의 러스트 프로젝트가 Cargo를 사용하기 때문에, 이 책의 나머지 부분에서는 여러분도 Cargo를 사용하고 있다고 가정합니다. Cargo는 ["설치하기"][installation]<!-- ignore --> 섹션에서 설명한 공식 설치 프로그램을 사용했다면 러스트와 함께 설치됩니다. 다른 방법으로 러스트를 설치했다면, 터미널에 다음을 입력하여 Cargo가 설치되어 있는지 확인하세요:

```console
$ cargo --version
```

버전 번호가 표시되면 설치되어 있는 것입니다! `command not found`와 같은 오류가 나타나면, 설치 방법 문서를 확인하여 Cargo를 별도로 설치하는 방법을 알아보세요.

### Cargo로 프로젝트 만들기

Cargo를 사용하여 새 프로젝트를 만들고 기존의 "Hello, world!" 프로젝트와 어떤 차이가 있는지 살펴보겠습니다. _projects_ 디렉터리(또는 코드를 저장하기로 결정한 어느 곳이든)로 돌아가세요. 그런 다음, 모든 운영 체제에서 다음을 실행하세요:

```console
$ cargo new hello_cargo
$ cd hello_cargo
```

첫 번째 명령어는 _hello_cargo_라는 새 디렉터리와 프로젝트를 생성합니다. 우리는 프로젝트 이름을 _hello_cargo_로 지었고, Cargo는 같은 이름의 디렉터리에 파일들을 생성합니다.

_hello_cargo_ 디렉터리로 이동하여 파일을 나열해보세요. Cargo가 두 개의 파일과 하나의 디렉터리를 생성한 것을 볼 수 있습니다: _Cargo.toml_ 파일과 _src_ 디렉터리 안에 _main.rs_ 파일이 있습니다.

또한 _.gitignore_ 파일과 함께 새 Git 저장소도 초기화되었습니다. 이미 존재하는 Git 저장소 내에서 `cargo new`를 실행하면 Git 파일은 생성되지 않습니다; `cargo new --vcs=git`을 사용하여 이 동작을 재정의할 수 있습니다.

> 참고: Git은 흔한 버전 관리 시스템입니다. `cargo new`가 다른 버전 관리 시스템을 사용하거나 버전 관리 시스템을 사용하지 않도록 `--vcs` 플래그를 사용하여 변경할 수 있습니다. 사용 가능한 옵션을 보려면 `cargo new --help`를 실행하세요.

선택한 텍스트 에디터로 _Cargo.toml_을 열어보세요. 코드 1-2와 비슷하게 보일 것입니다.

<Listing number="1-2" file-name="Cargo.toml" caption="*Cargo.toml*의 내용은 `cargo new`에 의해 생성됨">

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2024"

[dependencies]
```

</Listing>

이 파일은 [_TOML_][toml]<!-- ignore --> (_Tom's Obvious, Minimal Language_) 형식으로 되어 있으며, 이는 Cargo의 설정 형식입니다.

첫 번째 줄인 `[package]`는 패키지를 구성하는 문장들을 나타내는 섹션 제목입니다. 이 파일에 더 많은 정보를 추가하면 다른 섹션들도 추가할 것입니다.

다음 세 줄은 Cargo가 프로그램을 컴파일하는 데 필요한 구성 정보를 설정합니다: 이름, 버전, 그리고 사용할 러스트 에디션입니다. `edition` 키에 대해서는 [부록 E][appendix-e]<!-- ignore -->에서 이야기할 것입니다.

마지막 줄인 `[dependencies]`은 프로젝트의 의존성을 나열하는 섹션의 시작입니다. 러스트에서 코드 패키지는 _크레이트_라고 불립니다. 이 프로젝트에서는 다른 크레이트가 필요하지 않지만, 2장의 첫 프로젝트에서는 필요할 것이므로 그때 이 의존성 섹션을 사용할 것입니다.

이제 _src/main.rs_를 열고 살펴보세요:

<span class="filename">파일명: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo가 여러분을 위해 "Hello, world!" 프로그램을 생성했습니다. 코드 1-1에서 작성한 것과 똑같습니다! 지금까지 우리 프로젝트와 Cargo가 생성한 프로젝트의 차이점은 Cargo가 코드를 _src_ 디렉터리에 배치했고 최상위 디렉터리에 _Cargo.toml_ 설정 파일이 있다는 것입니다.

Cargo는 소스 파일이 _src_ 디렉터리 안에 있을 것으로 기대합니다. 최상위 프로젝트 디렉터리는 README 파일, 라이선스 정보, 설정 파일 및 코드와 관련 없는 다른 것들을 위한 곳입니다. Cargo를 사용하면 프로젝트를 정리하는 데 도움이 됩니다. 모든 것에 적합한 위치가 있으며, 모든 것이 제자리에 있게 됩니다.

"Hello, world!" 프로젝트처럼 Cargo를 사용하지 않는 프로젝트를 시작했다면, Cargo를 사용하는 프로젝트로 변환할 수 있습니다. 프로젝트 코드를 _src_ 디렉터리로 이동하고 적절한 _Cargo.toml_ 파일을 생성하세요. 해당 _Cargo.toml_ 파일을 얻는 가장 쉬운 방법은 `cargo init`을 실행하는 것입니다. 이 명령어는 자동으로 파일을 생성해 줍니다.

### Cargo 프로젝트 빌드하고 실행하기

이제 Cargo로 "Hello, world!" 프로그램을 빌드하고 실행할 때 어떤 차이가 있는지 살펴보겠습니다! _hello_cargo_ 디렉터리에서 다음 명령어를 입력하여 프로젝트를 빌드하세요:

```console
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

이 명령어는 현재 디렉터리가 아닌 _target/debug/hello_cargo_(또는 Windows에서는 _target\debug\hello_cargo.exe_)에 실행 파일을 생성합니다. 기본 빌드는 디버그 빌드이기 때문에 Cargo는 바이너리를 _debug_라는 디렉터리에 넣습니다. 다음 명령어로 실행 파일을 실행할 수 있습니다:

```console
$ ./target/debug/hello_cargo # 또는 Windows에서는 .\target\debug\hello_cargo.exe
Hello, world!
```

모든 것이 잘 된다면, `Hello, world!`가 터미널에 출력되어야 합니다. `cargo build`를 처음 실행하면 Cargo가 최상위 수준에 새 파일인 _Cargo.lock_을 생성합니다. 이 파일은 프로젝트의 의존성의 정확한 버전을 추적합니다. 이 프로젝트는 의존성이 없기 때문에 파일이 다소 희박합니다. 이 파일을 직접 수정할 필요는 전혀 없습니다; Cargo가 내용을 관리해줍니다.

우리는 방금 `cargo build`로 프로젝트를 빌드하고 `./target/debug/hello_cargo`로 실행했지만, `cargo run`을 사용하여 코드를 컴파일한 다음 한 명령어로 결과 실행 파일을 모두 실행할 수도 있습니다:

```console
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

`cargo run`을 사용하는 것이 `cargo build`를 실행한 다음 바이너리의 전체 경로를 사용하는 것보다 더 편리하므로, 대부분의 개발자는 `cargo run`을 사용합니다.

이번에는 Cargo가 `hello_cargo`를 컴파일하고 있다는 출력을 보지 못했다는 것을 주목하세요. Cargo는 파일이 변경되지 않았음을 파악했으므로, 다시 빌드하지 않고 바이너리를 실행했습니다. 소스 코드를 수정했다면, Cargo는 실행하기 전에 프로젝트를 다시 빌드했을 것이고, 다음과 같은 출력을 볼 수 있었을 것입니다:

```console
$ cargo run
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Cargo는 또한 `cargo check`라는 명령어를 제공합니다. 이 명령어는 코드가 컴파일되는지 빠르게 확인하지만 실행 파일을 생성하지는 않습니다:

```console
$ cargo check
   Checking hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

왜 실행 파일을 원하지 않을까요? 종종 `cargo check`는 실행 파일을 생성하는 단계를 건너뛰기 때문에 `cargo build`보다 훨씬 빠릅니다. 코드를 작성하는 동안 지속적으로 작업을 확인한다면, `cargo check`를 사용하면 프로젝트가 여전히 컴파일되는지 알려주는 과정을 더 빠르게 할 수 있습니다! 그래서 많은 러스타시안들은 프로그램을 작성하면서 주기적으로 `cargo check`를 실행하여 컴파일되는지 확인합니다. 그런 다음 실행 파일을 사용할 준비가 되었을 때 `cargo build`를 실행합니다.

지금까지 Cargo에 대해 배운 내용을 정리해 보겠습니다:

- `cargo new`를 사용하여 프로젝트를 생성할 수 있습니다.
- `cargo build`를 사용하여 프로젝트를 빌드할 수 있습니다.
- `cargo run`을 사용하여 한 단계로 프로젝트를 빌드하고 실행할 수 있습니다.
- `cargo check`를 사용하여 오류를 확인하기 위해 바이너리를 생성하지 않고 프로젝트를 빌드할 수 있습니다.
- 빌드 결과를 코드와 같은 디렉터리에 저장하는 대신, Cargo는 _target/debug_ 디렉터리에 저장합니다.

Cargo를 사용하는 추가적인 이점은 어떤 운영 체제에서 작업하든 명령어가 동일하다는 것입니다. 따라서 이 시점부터는 Linux와 macOS 대 Windows에 대한 특정 지침을 더 이상 제공하지 않을 것입니다.

### 릴리스 빌드하기

프로젝트가 마침내 릴리스할 준비가 되면, `cargo build --release`를 사용하여 최적화로 컴파일할 수 있습니다. 이 명령어는 _target/debug_ 대신 _target/release_에 실행 파일을 생성합니다. 최적화는 러스트 코드를 더 빠르게 실행되게 만들지만, 최적화를 켜면 프로그램을 컴파일하는 데 더 많은 시간이 걸립니다. 이것이 두 가지 다른 프로필이 있는 이유입니다: 빠르고 자주 다시 빌드하려는 개발용과, 반복적으로 다시 빌드되지 않고 가능한 한 빠르게 실행될 사용자에게 제공할 최종 프로그램을 위한 빌드용입니다. 코드의 실행 시간을 벤치마킹하고 있다면, `cargo build --release`를 실행하고 _target/release_의 실행 파일로 벤치마크하세요.

### 관례로서의 Cargo

단순한 프로젝트에서는 Cargo가 `rustc`를 직접 사용하는 것보다 많은 가치를 제공하지 않지만, 프로그램이 더 복잡해짐에 따라 그 가치가 증명될 것입니다. 프로그램이 여러 파일로 성장하거나 의존성이 필요할 때, 빌드를 조정하는 것을 Cargo에게 맡기는 것이 훨씬 쉽습니다.

`hello_cargo` 프로젝트가 단순하더라도, 이제 앞으로의 러스트 경력에서 사용할 실제 도구의 대부분을 사용하고 있습니다. 사실, 기존 프로젝트에서 작업하려면 다음 명령어를 사용하여 Git으로 코드를 체크아웃하고, 해당 프로젝트의 디렉터리로 변경한 다음, 빌드할 수 있습니다:

```console
$ git clone example.org/someproject
$ cd someproject
$ cargo build
```

Cargo에 대한 더 많은 정보를 원하시면, [문서][cargo]를 확인하세요.

## 요약

여러분은 이미 러스트 여정을 훌륭하게 시작했습니다! 이 장에서는 다음을 배웠습니다:

- `rustup`을 사용하여 최신 안정 버전의 러스트 설치하기
- 새로운 러스트 버전으로 업데이트하기
- 로컬에 설치된 문서 열기
- `rustc`를 직접 사용하여 "Hello, world!" 프로그램 작성 및 실행하기
- Cargo의 관례를 사용하여 새 프로젝트 생성 및 실행하기

이제는 더 실질적인 프로그램을 만들어서 러스트 코드 읽기와 작성에 익숙해질 좋은 시간입니다. 그래서 2장에서는 추리 게임 프로그램을 만들 것입니다. 만약 먼저 러스트에서 일반적인 프로그래밍 개념이 어떻게 작동하는지 배우고 싶다면, 3장을 보고 나서 2장으로 돌아오세요.

[installation]: ch01-01-installation.html#installation
[toml]: https://toml.io
[appendix-e]: appendix-05-editions.html
[cargo]: https://doc.rust-lang.org/cargo/