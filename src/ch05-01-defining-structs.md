## 구조체 정의와 인스턴스 생성

구조체는 [“튜플 타입”][tuples]<!-- ignore -->에서 다룬 튜플과 비슷하게, 여러 관련 값을 담을 수 있습니다. 튜플과 마찬가지로, 구조체의 각 부분은 서로 다른 타입일 수 있습니다. 하지만 튜플과 달리, 구조체에서는 각 데이터 조각에 이름을 붙여 그 값이 무엇을 의미하는지 명확하게 할 수 있습니다. 이름을 붙이면 구조체가 튜플보다 더 유연해집니다. 데이터의 순서에 의존하지 않고 인스턴스의 값을 지정하거나 접근할 수 있기 때문입니다.

구조체를 정의하려면 `struct` 키워드와 구조체 전체의 이름을 입력합니다. 구조체 이름은 함께 묶는 데이터의 의미를 잘 설명해야 합니다. 중괄호 안에는 데이터 조각의 이름과 타입을 정의하는데, 이를 _필드_라고 부릅니다. 예를 들어, 리스트 5-1은 사용자 계정 정보를 저장하는 구조체를 보여줍니다.

<Listing number="5-1" file-name="src/main.rs" caption="`User` 구조체 정의">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-01/src/main.rs:here}}
```

</Listing>

구조체를 정의한 뒤에는, 각 필드에 구체적인 값을 지정하여 구조체의 _인스턴스_를 만들 수 있습니다. 인스턴스를 만들려면 구조체 이름을 쓰고, 중괄호 안에 _`키: 값`_ 쌍을 넣습니다. 키는 필드 이름이고, 값은 해당 필드에 저장할 데이터입니다. 필드의 순서는 구조체 정의와 달라도 됩니다. 즉, 구조체 정의는 타입의 일반적인 템플릿이고, 인스턴스는 그 템플릿을 실제 데이터로 채워 타입의 값을 만드는 것입니다. 예를 들어, 리스트 5-2처럼 특정 사용자를 선언할 수 있습니다.

<Listing number="5-2" file-name="src/main.rs" caption="`User` 구조체 인스턴스 생성">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-02/src/main.rs:here}}
```

</Listing>

구조체에서 특정 값을 얻으려면 점 표기법을 사용합니다. 예를 들어, 이 사용자의 이메일 주소에 접근하려면 `user1.email`을 사용합니다. 인스턴스가 가변이면, 점 표기법으로 특정 필드에 값을 할당하여 변경할 수 있습니다. 리스트 5-3은 가변 `User` 인스턴스의 `email` 필드 값을 변경하는 방법을 보여줍니다.

<Listing number="5-3" file-name="src/main.rs" caption="`User` 인스턴스의 `email` 필드 값 변경하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-03/src/main.rs:here}}
```

</Listing>

구조체 인스턴스 전체가 가변이어야 한다는 점에 주의하세요. 러스트는 특정 필드만 가변으로 지정하는 것을 허용하지 않습니다. 모든 표현식과 마찬가지로, 함수 본문의 마지막 표현식에서 구조체의 새 인스턴스를 만들어 암묵적으로 반환할 수도 있습니다.

리스트 5-4는 주어진 이메일과 사용자 이름으로 `User` 인스턴스를 반환하는 `build_user` 함수를 보여줍니다. `active` 필드는 `true`로, `sign_in_count`는 `1`로 설정됩니다.

<Listing number="5-4" file-name="src/main.rs" caption="이메일과 사용자 이름을 받아 `User` 인스턴스를 반환하는 `build_user` 함수">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-04/src/main.rs:here}}
```

</Listing>

함수 매개변수 이름을 구조체 필드 이름과 동일하게 지정하는 것이 자연스럽지만, `email`과 `username` 필드 이름과 변수를 반복해서 써야 하는 점이 다소 번거롭습니다. 만약 구조체에 더 많은 필드가 있다면, 각 이름을 반복하는 것이 더 귀찮아질 것입니다. 다행히도, 더 편리한 축약 문법이 있습니다!

<!-- Old heading. Do not remove or links may break. -->

<a id="using-the-field-init-shorthand-when-variables-and-fields-have-the-same-name"></a>

### 필드 초기화 축약 문법 사용하기

리스트 5-4에서처럼 매개변수 이름과 구조체 필드 이름이 정확히 같을 때는, _필드 초기화 축약 문법_을 사용하여 `build_user`를 반복 없이 더 간결하게 쓸 수 있습니다. 리스트 5-5를 참고하세요.

<Listing number="5-5" file-name="src/main.rs" caption="`username`과 `email` 매개변수가 구조체 필드와 이름이 같으므로 필드 초기화 축약 문법을 사용하는 `build_user` 함수">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-05/src/main.rs:here}}
```

</Listing>

여기서는 `User` 구조체의 새 인스턴스를 만들고 있습니다. `email` 필드의 값을 `build_user` 함수의 `email` 매개변수 값으로 설정하고 싶습니다. 필드 이름과 매개변수 이름이 같으므로, `email: email` 대신 `email`만 써도 됩니다.

### 구조체 업데이트 문법으로 다른 인스턴스에서 값 복사하기

동일한 타입의 다른 인스턴스에서 대부분의 값을 복사하고 일부만 변경하여 새 구조체 인스턴스를 만드는 것이 유용할 때가 많습니다. 이때는 _구조체 업데이트 문법_을 사용할 수 있습니다.

먼저, 리스트 5-6에서는 구조체 업데이트 문법 없이 `user2`라는 새 `User` 인스턴스를 만드는 방법을 보여줍니다. `email`에는 새 값을 지정하고, 나머지 값은 리스트 5-2에서 만든 `user1`의 값을 사용합니다.

<Listing number="5-6" file-name="src/main.rs" caption="`user1`의 값 중 하나만 바꿔 새 `User` 인스턴스 만들기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-06/src/main.rs:here}}
```

</Listing>

구조체 업데이트 문법을 사용하면 더 적은 코드로 같은 효과를 낼 수 있습니다. 리스트 5-7을 참고하세요. `..` 문법은 명시적으로 지정하지 않은 나머지 필드에 주어진 인스턴스의 값을 사용하라는 뜻입니다.

<Listing number="5-7" file-name="src/main.rs" caption="구조체 업데이트 문법으로 `User` 인스턴스의 `email`만 새 값으로 지정하고 나머지는 `user1`에서 복사하기">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-07/src/main.rs:here}}
```

</Listing>

리스트 5-7의 코드는 `user2` 인스턴스를 만들 때 `email`만 다르고, `username`, `active`, `sign_in_count`는 모두 `user1`에서 가져옵니다. `..user1`은 마지막에 와야 하며, 나머지 필드에 `user1`의 값을 사용하라는 의미입니다. 원하는 만큼 필드를 어떤 순서로든 지정할 수 있습니다. 구조체 정의의 필드 순서와 상관없습니다.

구조체 업데이트 문법은 `=`를 사용하므로 할당처럼 보이지만, 실제로는 데이터를 _이동_시키는 동작입니다. [“변수와 데이터의 이동”][move]<!-- ignore -->에서 본 것처럼, 이 예제에서 `user2`를 만든 뒤에는 `user1`을 더 이상 사용할 수 없습니다. 왜냐하면 `user1`의 `username` 필드에 있던 `String`이 `user2`로 이동했기 때문입니다. 만약 `user2`의 `email`과 `username` 모두 새 `String` 값으로 지정했다면, `user1`도 계속 사용할 수 있습니다. `active`와 `sign_in_count`는 `Copy` 트레이트를 구현한 타입이므로, [“스택 전용 데이터: Copy”][copy]<!-- ignore -->에서 설명한 동작이 적용됩니다. 이 예제에서는 `user1.email`을 계속 사용할 수 있는데, 그 값은 이동되지 않았기 때문입니다.

### 필드 이름 없이 타입만 있는 튜플 구조체로 다른 타입 만들기

러스트는 튜플과 비슷한 구조체도 지원합니다. 이를 _튜플 구조체_라고 부릅니다. 튜플 구조체는 구조체 이름이 의미를 더해주지만, 필드에는 이름이 없고 타입만 있습니다. 튜플 구조체는 전체 튜플에 이름을 붙이고, 다른 튜플과 타입을 다르게 만들고 싶을 때, 그리고 각 필드에 이름을 붙이는 것이 번거롭거나 불필요할 때 유용합니다.

튜플 구조체를 정의하려면 `struct` 키워드와 구조체 이름 뒤에 튜플의 타입을 나열합니다. 예를 들어, 아래는 `Color`와 `Point`라는 두 튜플 구조체를 정의하고 사용하는 예시입니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-01-tuple-structs/src/main.rs}}
```

</Listing>

`black`과 `origin` 값은 서로 다른 타입입니다. 각각 다른 튜플 구조체의 인스턴스이기 때문입니다. 구조체를 정의하면, 필드 타입이 같더라도 각 구조체는 고유한 타입이 됩니다. 예를 들어, `Color` 타입의 매개변수를 받는 함수는 `Point`를 인자로 받을 수 없습니다. 두 타입 모두 세 개의 `i32` 값을 가지지만, 타입이 다르기 때문입니다. 그 외에는 튜플 구조체 인스턴스도 튜플처럼 각 부분을 분해할 수 있고, `.` 뒤에 인덱스를 붙여 개별 값에 접근할 수 있습니다. 튜플과 달리, 튜플 구조체를 분해할 때는 구조체 타입 이름을 반드시 명시해야 합니다. 예를 들어, `let Point(x, y, z) = origin;`처럼 작성해야 합니다.

### 필드가 없는 유닛 구조체

필드가 전혀 없는 구조체도 정의할 수 있습니다! 이를 _유닛 구조체_라고 하며, [“튜플 타입”][tuples]<!-- ignore -->에서 언급한 `()` 유닛 타입과 비슷하게 동작합니다. 유닛 구조체는 타입에 저장할 데이터가 없지만, 어떤 타입에 트레이트를 구현해야 할 때 유용합니다. 트레이트에 대해서는 10장에서 다룹니다. 아래는 `AlwaysEqual`이라는 유닛 구조체를 선언하고 인스턴스화하는 예시입니다:

<Listing file-name="src/main.rs">

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-04-unit-like-structs/src/main.rs}}
```

</Listing>

`AlwaysEqual`을 정의하려면 `struct` 키워드와 원하는 이름, 그리고 세미콜론만 쓰면 됩니다. 중괄호나 괄호는 필요 없습니다! 인스턴스도 마찬가지로 이름만 써서 만들 수 있습니다. 나중에 이 타입에 모든 인스턴스가 항상 서로 같다는 동작을 구현한다고 상상해 보세요. 테스트 목적 등으로 결과를 고정하고 싶을 때 데이터가 필요하지 않을 수도 있습니다! 10장에서 트레이트를 정의하고, 유닛 구조체를 포함한 모든 타입에 트레이트를 구현하는 방법을 배웁니다.

> ### 구조체 데이터의 소유권
>
> 리스트 5-1의 `User` 구조체 정의에서는 빌림 문자열 슬라이스 타입 `&str` 대신 소유권을 가진 `String` 타입을 사용했습니다. 이는 구조체의 각 인스턴스가 모든 데이터를 소유하고, 구조체 전체가 유효한 동안 데이터도 유효하도록 하기 위한 의도적인 선택입니다.
>
> 구조체가 다른 곳에서 소유한 데이터에 대한 참조를 저장할 수도 있지만, 그러려면 _수명_이라는 러스트 기능을 사용해야 합니다. 수명은 구조체가 참조하는 데이터가 구조체가 유효한 동안 항상 유효하도록 보장합니다. 만약 수명을 지정하지 않고 구조체에 참조를 저장하려고 하면, 아래와 같이 동작하지 않습니다:
>
> <Listing file-name="src/main.rs">
>
> <!-- CAN'T EXTRACT SEE https://github.com/rust-lang/mdBook/issues/1127 -->
>
> ```rust,ignore,does_not_compile
> struct User {
>     active: bool,
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
> }
>
> fn main() {
>     let user1 = User {
>         active: true,
>         username: "someusername123",
>         email: "someone@example.com",
>         sign_in_count: 1,
>     };
> }
> ```
>
> </Listing>
>
> 컴파일러는 수명 지정자가 필요하다고 경고합니다:
>
> ```console
> $ cargo run
>    Compiling structs v0.1.0 (file:///projects/structs)
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:3:15
>   |
> 3 |     username: &str,
>   |               ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 ~     username: &'a str,
>   |
>
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:4:12
>   |
> 4 |     email: &str,
>   |            ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 |     username: &str,
> 4 ~     email: &'a str,
>   |
>
> For more information about this error, try `rustc --explain E0106`.
> error: could not compile `structs` (bin "structs") due to 2 previous errors
> ```
>
> 10장에서 이런 오류를 해결하여 구조체에 참조를 저장하는 방법을 다룹니다. 지금은 참조 대신 소유권을 가진 타입인 `String`을 사용하여 오류를 피합니다.

<!-- manual-regeneration
for the error above
after running update-rustc.sh:
pbcopy < listings/ch05-using-structs-to-structure-related-data/no-listing-02-reference-in-struct/output.txt
paste above
add `> ` before every line -->

[tuples]: ch03-02-data-types.html#the-tuple-type
[move]: ch04-01-what-is-ownership.html#variables-and-data-interacting-with-move
[copy]: ch04-01-what-is-ownership.html#stack-only-data-copy
