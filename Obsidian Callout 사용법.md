---
publishName: how-to-use-obsidian-callout
url: https://help.obsidian.md/How+to/Use+callouts
---

# Obsidian Callout 사용법

마크다운에서 quotation에 사용하는 `>` 기호를 이용하여 callout을 만들 수 있다.

```md
> [!note]
> note callout입니다.
```

> [!note]
> note callout입니다.

## 사용할 수 있는 Callout

종류는 총 13가지이며, alias가 있는 callout도 있다.

- note
- abstract, summary, tldr
- info
- todo
- tip, hint, important
- success, check, done
- question, help, faq
- warning, caution, attention
- failure, fail, missing
- danger, error
- bug
- example
- quote, cite

## Callout 제목 지정하기

callout에 제목을 지정할 수 있다.

```md
> [!note] 이 callout은 이 제목으로 표시됩니다.
> 노트 내용
```

> [!note] 이 callout은 이 제목으로 표시됩니다.
> 노트 내용

한편, callout의 이름을 내 마음대로 지정하면 자동으로 제목이 있는 note callout이 생성된다. 아래 예시는 바로 위 예시와 완전히 똑같이 동작한다.

```md
> [!이 callout은 이 제목으로 표시됩니다.]
> 노트 내용
```

## Callout에 접기 버튼 추가하기

callout에 접기 버튼을 추가할 수도 있다. 내용이 길다면 요긴하다.

```md
> [!note]-
> 이 callout은 접을 수 있습니다.
```

> [!note]-
> 이 callout은 접을 수 있습니다.

접는 callout은 에디터 모드에서는 확인해볼 수 없고 미리보기에서 눌러볼 수 있다.