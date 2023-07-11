# vim에서 특정 범위의 문자열 치환하기

```
# 4~10번 라인의 foo를 bar로 교체
:4,10s/foo/bar

# 현재 커서 위치 기준으로 부모 중괄호 안의 foo를 bar로 교체
:vi{:s/foo/bar
```
