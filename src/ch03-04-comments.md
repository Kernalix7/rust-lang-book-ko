## 주석

모든 프로그래머는 자신의 코드를 이해하기 쉽게 만들고자 노력하지만, 때로는 추가 설명이 필요할 때가 있습니다. 이런 경우, 프로그래머들은 소스 코드에 _주석_을 남깁니다. 주석은 컴파일러가 무시하지만, 소스 코드를 읽는 사람에게는 유용할 수 있습니다.

아래는 간단한 주석 예시입니다:

```rust
// hello, world
```

러스트에서 관용적인 주석 스타일은 두 개의 슬래시로 주석을 시작하며, 주석은 줄 끝까지 이어집니다. 한 줄을 넘는 주석을 작성하려면 각 줄마다 `//`를 붙여야 합니다:

```rust
// 여기서 복잡한 작업을 하고 있으니, 설명이 길어져서
// 여러 줄의 주석이 필요합니다! 휴! 이 주석이
// 무슨 일이 벌어지는지 설명해 주길 바랍니다.
```

주석은 코드가 있는 줄의 끝에도 작성할 수 있습니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-24-comments-end-of-line/src/main.rs}}
```

하지만 주석은 주로 코드 위의 별도 줄에 작성하는 형식으로 더 자주 사용됩니다:

<span class="filename">파일명: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-25-comments-above-line/src/main.rs}}
```

러스트에는 또 다른 종류의 주석인 문서화 주석도 있습니다. 문서화 주석에 대해서는 [14장 "Crates.io에 크레이트 배포하기"][publishing]<!-- ignore -->에서 다룹니다.

[publishing]: ch14-02-publishing-to-crates-io.html