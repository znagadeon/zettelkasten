---
publishName: z-index-quiz
url: https://dev.epiloum.net/904
---

# 퀴즈로 풀어보는 CSS z-index 속성의 3가지 포인트

Pug로 이런 HTML을 작성했다고 가정하자. CSS가 다음과 같다면, 어떤 결과물이 나올까?

```pug
#first
#second
  #third
```

## Q1

```css
#first { z-index: 30 }
#second { z-index: 20 }
#third { z-index: 50 }
```

- 정답: `#first` < `#second` < `#third`
- 이유: `z-index`는 `position`이 `static`이 아닐 때만 동작하므로, 해당 css에서 `z-index` 값은 무시되고 쌓인 순서대로 보임
  - `position`이 정의되어 있지 않다면 기본값은 `static`

## Q2

```css
#first {
  position: relative;
  z-index: 30;
}
#second {
  position: relative;
  z-index: 20;
}
#third {
  position: relative;
  z-index: 50;
}
```

- 정답: `#second` < `#third` < `#first`
- 이유: `z-index`는 기본적으로 형제간에만 비교하므로
  - `#first`는 `z-index`가 `#second`보다 높으므로 `#second`보다 위로 감
  - `#third`는 `#second`의 자식 요소이므로 `#second`보다 위로 감
  - `#third`의 부모인 `#second`의 `z-index`가 `#first`의 `z-index`보다 작으므로 `#first`가 `#third`보다 위로 감

## Q3

```css
#first {
  position: relative;
  z-index: 30;
}
#second {
  position: relative;
  /* z-index: 20; */
}
#third {
  position: relative;
  z-index: 50;
}
```

- 정답: `#second` < `#first` < `#third`
- 이유: 부모에게 `z-index`가 적용되어 있지 않다면, 자식 엘리먼트만 독립적으로 `z-index`를 비교하므로
  - `z-index`가 정의되어 있지 않다면 기본값은 `auto`
  - `z-index`가 `auto`인 부모 아래에 정의된 자식 엘리먼트들은 부모의 형제 엘리먼트들과 `z-index`를 직접 비교할 수 있음

> [!note]
> - IE 7의 `z-index` 기본값은 0
> - 따라서, IE 7에서는 `#second` < `#third` < `#first`가 정답이 됨