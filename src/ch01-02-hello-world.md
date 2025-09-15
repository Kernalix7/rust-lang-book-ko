## Hello, World!

러스트를 설치했다면, 이제 첫 번째 러스트 프로그램을 작성해 봅시다. 이 장에서는 "Hello, world!"를 출력하는 프로그램을 만들고, 컴파일하고, 실행하는 과정을 안내합니다.

### 새 러스트 프로젝트 만들기

먼저, 러스트 프로젝트를 위한 새 디렉터리를 만듭니다. 터미널에서 다음 명령어를 입력하세요:

```console
$ mkdir projects
$ cd projects
$ mkdir hello_world
$ cd hello_world
```

이제 _hello_world_ 디렉터리 안에 _main.rs_라는 파일을 만듭니다. 텍스트 에디터로 _main.rs_를 열고 아래 코드를 입력하세요:

<span class="filename">파일명: main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

이 코드는 러스트 프로그램의 진입점인 `main` 함수를 정의합니다. 중괄호 `{}` 안에는 프로그램이 실행될 때 수행할 코드가 들어갑니다. 여기서는 `println!` 매크로를 사용해 "Hello, world!"를 출력합니다.

### 프로그램 컴파일 및 실행하기

이제 프로그램을 컴파일해 봅시다. 터미널에서 다음 명령어를 입력하세요:

```console
$ rustc main.rs
```

이 명령어는 _main.rs_ 파일을 컴파일하여 실행 파일을 생성합니다. Linux와 macOS에서는 _hello_world_라는 실행 파일이, Windows에서는 _hello_world.exe_가 생성됩니다.

이제 프로그램을 실행해 보세요:

```console
$ ./hello_world # 또는 Windows에서는 .\hello_world.exe
Hello, world!
```

터미널에 "Hello, world!"가 출력되면, 러스트 프로그램이 정상적으로 동작하는 것입니다!

### 코드 분석

이제 코드의 각 부분을 자세히 살펴봅시다.

- `fn main() { ... }`는 러스트 프로그램의 진입점인 함수를 정의합니다.
- `println!`은 러스트의 매크로로, 괄호 안의 문자열을 출력합니다. 매크로는 함수와 비슷하지만, 느낌표(`!`)로 구분됩니다.

러스트에서는 모든 프로그램이 반드시 `main` 함수를 가져야 합니다. `println!` 매크로는 표준 출력에 텍스트를 출력하는 데 사용됩니다.

### 요약

축하합니다! 러스트로 첫 번째 프로그램을 성공적으로 작성하고 실행했습니다. 이제 러스트의 빌드 시스템과 패키지 매니저인 Cargo를 사용하는 방법을 배워봅시다.
