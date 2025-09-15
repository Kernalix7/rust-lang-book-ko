## 모듈 트리의 항목을 참조하기 위한 경로

러스트의 모듈 시스템에서는 크레이트 내의 항목(함수, 구조체, 열거형 등)에 접근할 때 _경로(path)_를 사용합니다. 경로는 모듈 트리 내에서 항목의 위치를 지정하는 방식입니다. 이번 장에서는 경로의 종류, 경로를 사용하는 방법, 그리고 경로의 공개 범위를 제어하는 `pub` 키워드에 대해 다룹니다.

경로는 두 가지 방식으로 작성할 수 있습니다: _절대 경로_와 _상대 경로_입니다.

- **절대 경로**는 크레이트 루트에서 시작하며, `crate` 키워드로 시작합니다.
- **상대 경로**는 현재 모듈에서 시작하며, 현재 위치를 기준으로 항목을 참조합니다.

예를 들어, 아래와 같은 모듈 구조가 있다고 가정해 봅시다:

```text
crate
 └── front_of_house
     └── hosting
         └── add_to_waitlist
```

`add_to_waitlist` 함수에 접근하려면, 절대 경로로는 `crate::front_of_house::hosting::add_to_waitlist`를 사용할 수 있습니다. 상대 경로로는 현재 위치에 따라 `front_of_house::hosting::add_to_waitlist` 또는 `hosting::add_to_waitlist`처럼 작성할 수 있습니다.

경로를 사용할 때는 `::` 연산자를 사용하여 각 모듈과 항목을 구분합니다. 예를 들어, 아래와 같이 함수 호출이 가능합니다:

```rust
crate::front_of_house::hosting::add_to_waitlist();
```

또는 상대 경로를 사용할 수도 있습니다:

```rust
front_of_house::hosting::add_to_waitlist();
```

경로를 사용할 때, 항목의 공개 범위에 따라 접근이 제한될 수 있습니다. 기본적으로 모듈과 그 내부 항목은 비공개입니다. 외부에서 접근하려면 `pub` 키워드를 사용하여 공개로 지정해야 합니다.

예를 들어, 아래와 같이 모듈과 함수를 공개로 지정할 수 있습니다:

```rust
pub mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            // ...기존 코드...
        }
    }
}
```

이렇게 하면, 크레이트 외부나 다른 모듈에서 `add_to_waitlist` 함수에 접근할 수 있습니다.

### `super`와 `self` 키워드

경로를 지정할 때, 현재 모듈을 기준으로 상대 경로를 작성할 수 있습니다.  
- `self`는 현재 모듈을 의미합니다.
- `super`는 부모 모듈을 의미합니다.

예를 들어, 아래와 같은 구조에서:

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            // ...기존 코드...
        }
    }
    pub fn serve_customer() {
        // ...기존 코드...
        hosting::add_to_waitlist();
        super::deliver_order();
    }
}

fn deliver_order() {
    // ...기존 코드...
}
```

`serve_customer` 함수에서 `hosting::add_to_waitlist()`는 같은 모듈 내의 하위 모듈을 참조하는 상대 경로입니다. `super::deliver_order()`는 부모 모듈의 함수를 참조합니다.

### `pub` 키워드로 경로 공개하기

기본적으로 모듈과 그 내부 항목은 비공개입니다. 외부에서 접근하려면 `pub` 키워드를 사용해야 합니다.  
- `pub mod`로 모듈을 공개하면, 해당 모듈을 외부에서 참조할 수 있습니다.
- `pub fn`으로 함수를 공개하면, 해당 함수에 외부에서 접근할 수 있습니다.
- 구조체와 열거형도 `pub struct`, `pub enum`으로 공개할 수 있습니다.

예를 들어, 아래와 같이 작성하면:

```rust
pub mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            // ...기존 코드...
        }
    }
}
```

이제 `crate::front_of_house::hosting::add_to_waitlist()`로 외부에서 함수에 접근할 수 있습니다.

구조체의 경우, 구조체 자체를 `pub`으로 공개해도 필드가 비공개라면 외부에서 필드에 접근할 수 없습니다. 필드도 `pub`으로 지정해야 외부에서 접근 가능합니다.

```rust
pub struct Breakfast {
    pub toast: String,
    seasonal_fruit: String,
}
```

여기서 `toast` 필드는 외부에서 접근할 수 있지만, `seasonal_fruit` 필드는 비공개입니다.

열거형의 경우, 열거형을 `pub`으로 공개하면 모든 변형도 자동으로 공개됩니다.

```rust
pub enum Appetizer {
    Soup,
    Salad,
}
```

이렇게 하면, 외부에서 `Appetizer::Soup`와 `Appetizer::Salad`를 사용할 수 있습니다.

### 요약

- 경로는 크레이트 내 항목의 위치를 지정하는 방식입니다.
- 절대 경로는 `crate`에서 시작하며, 상대 경로는 현재 모듈에서 시작합니다.
- 경로를 사용할 때는 `::` 연산자를 사용합니다.
- 기본적으로 모듈과 항목은 비공개이며, `pub` 키워드로 공개할 수 있습니다.
- `self`와 `super` 키워드를 사용하여 상대 경로를 지정할 수 있습니다.
- 구조체의 필드는 별도로 `pub`을 지정해야 외부에서 접근할 수 있습니다.
- 열거형을 `pub`으로 공개하면 모든 변형도 공개됩니다.