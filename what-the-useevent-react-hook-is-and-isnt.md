---
aliases:
  - useEvent 리액트 훅이란(또 무엇이 아닌지)
url: https://velog.io/@eunbinn/what-the-useevent-react-hook-is-and-isnt
---

# `useEvent` 리액트 훅이란(또 무엇이 아닌지)

## 지금까지의 문제

### 예시 1: 너무 자주 갱신되는 이벤트 함수

- `msg`가 변경될 때마다 `onClick` 함수가 재생성됨
  - 해당 함수를 JSX에서 사용하고 있으므로, `Chat` 컴포넌트가 계속 리렌더링됨

```tsx
const Chat = () => {
  const [msg, setMsg] = useState('');

  const onChange = (value: string) => {
    setMsg(value);
  };

  const onClick = () => {
    sendMessage(msg);
  };

  return (
    <>
      <input onChange={e => onChange(e.target.value)} />
      <button onClick={onClick}>Send Message</button>
    </>
  );
};
```

### 예시 2: 잘못된 시점에 실행되는 `useEffect`

- 사용자의 이름이 변경될 때도 로깅이 발생함
- 의존성 배열에서 `name`을 빼면 되지만, 추적하기 어려운 버그(Stale closure)가 발생할 위험 있음

```tsx
const Page = ({ route, currentUser }) => {
  useEffect(() => {
    logAnalytics('visit-page', route.url, currentUser.name);
  }, [route.url, currentUser.name]);
};
```

## `useEvent`로 문제 해결하기

지금까지는 이런 문제가 발생하면 `useRef`를 이용해 회피했지만, `useEvent`를 이용해 간단히 해결할 수 있음

```tsx
const Chat = () => {
  // useRef를 이용한 방법
  const msg = useRef('');

  const onChange = (value: string) => {
    msg.current = value;
  };

  const onClick = () => {
    sendMessage(msg.current);
  };

  // useEvent를 이용한 방법
  const onClick = useEvent(() => {
    sendMessage(msg); // 이제 msg가 변경되더라도 onClick이 재생성되지 않음
  });
};
```

```tsx
const Page = ({ route, currentUser }) => {
  // useRef를 이용한 방법
  const name = useRef('');

  useEffect(() => {
    name.current = currentUser.name;
  }, [currentUser.name]);

  useEffect(() => {
    logAnalytics('visit-page', route.url, name.current);
  }, [route.url]);

  // useEvent를 이용한 방법
  const log = useEvent((url: string) => {
    logAnalytics('visit-page', url, currentUser.name);
  });

  useEffect(() => {
    log(route.url);
  }, [route.url]); // <- currentUser.name이 의존성에서 제거됨
};
```
