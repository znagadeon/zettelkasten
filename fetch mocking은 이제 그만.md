---
publishName: stop-mocking-fetch
url: https://velog.io/@dannyworks102/fetch-mocking%EC%9D%80-%EC%9D%B4%EC%A0%9C-%EA%B7%B8%EB%A7%8C
---

# fetch mocking은 이제 그만

## API를 테스트하는 기존 방법들

- API Client를 모킹하기
  - 내가 보낸 요청이 제대로 된 건지 알 수 없다
- `window.fetch`를 모킹하기
  - API Client가 제대로 동작함을 보장
  - 서버 상태가 완전히 모킹된 것이 아니므로, 서버에 붙었을 때 똑같이 동작하는지는 보장할 수 없음
  - 내가 `window.fetch`를 제대로 사용한 것인지 테스트 불가능
- E2E 테스트로 동작을 확인하고, 통합 테스트에서는 API Client 모킹하기
- API Client의 별도 유닛테스트를 작성하고, 통합테스트에서는 API Client 모킹하기

## `window.fetch` 모킹을 고도화하기

- `fetch`를 모킹하되, url을 기반으로 switch를 작성하여 마치 진짜 서버처럼 작동하도록 구현

```ts
export const mockFetch = async (url, config) => {
  if (url === '/login') {
    return {
      ok: true,
      status: 200,
      json: async () => ({ user }),
    };
  }
  if (url === '/checkout') {
    if (!isAuthorized) {
      return Promise.reject({
        ok: false,
        status: 401,
        json: async () => ({ message: 'Not authorized' }),
      });
    }

    return {
      ok: true,
      status: 200,
      json: async () => ({ success: true }),
    };
  }
  // ...
};
```

## MSW 사용하기

### MSW란?

- Mock Service Worker의 약자
- Node.js에서는 작동하지 않지만, 테스트 목적에 한해 Node.js를 지원하고 있음
- 테스트 환경과 개발환경에서 브라우저에서와 완전히 동일한 서버 핸들러를 사용할 수 있음
  - 데이터는 `faker`나 `test-data-bot` 같은 가짜 데이터를 사용하자

### 사용해 보기

```ts
// server.js
import { rest } from 'msw';
import * as users from './users';

export const handlers = [
  rest.get('/login', async (req, res, ctx) => {
    const users = await users.login(JSON.parse(req.body));
    return res(ctx.json({user}));
  }),
  rest.post('/checkout', async (req, res, ctx) => {
    const user = await user.login(JSON.parse(req.body));
    const isAuthorized = user.authorize(req.headers.Authorization);
    if (!isAuthorized) {
      return res(ctx.status(401), ctx.json({ message: 'Not Authorized' }));
    }
    return res(ctx.json({ success: true }));
  }),
];
```

```ts
// checkout.spec.ts
import { setupServer } from 'msw/node';
import { handlers } from './server';

const server = setupServer(...handlers);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('Checkouts when clicking button', () => {
  render(<LoginButton />);
  userEvent.click(screen.getByRole('button', { name: /Checkout/i }));

  expect(await screen.findByText(/success/i)).toBeInTheDocument();
});
```

#### 동적으로 핸들러 추가하기

- 해당 테스트에서만 사용하는 특이한 엣지 케이스의 경우, 서버 핸들러에 전부 몰아넣는 대신 테스트케이스에서 따로 정의하는 것이 가능
- `afterEach`의 `resetHandler`에 의해, 테스트가 끝난 뒤 자동으로 임시 핸들러는 삭제됨

```ts
test('Shows server error message when request fails', () => {
  const testErrorMessage = 'Test Failure';
  server.use(
    rest.post('/checkout', asynㅌ (req, res, ctx) => {
      return res(ctx.status(500), ctx.json({ message: testErrorMessage }));
    });
  );

  // ...
  expect(await screen.findByRole('alert')).toHaveTextContent(testErrorMessage);
});
```

### 비슷한 라이브러리

- `nock`
- `miragejs`
  - `miragejs`는 `fetch`와 `XMLHJttpRequest`를 몽키패칭하여, 실제로 브라우저가 서버로 요청을 보내는 것은 아님
  - MSW는 브라우저 레벨에서 요청을 가로채므로, 개발자 도구의 네트워크 탭에서 요청을 확인할 수 있음
  - MSW보다는 `miragejs` 쪽이 좀 더 편리하게 사용 가능
