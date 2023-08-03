# wc로 line count 세기

```sh
wc -l file.txt
echo test | wc -l
```

응용하여, 현재 브랜치가 베이스 브랜치 대비 얼마나 변경되었는지도 셀 수 있다.

```sh
git diff develop | grep -E "^[+-]" | wc -l
```