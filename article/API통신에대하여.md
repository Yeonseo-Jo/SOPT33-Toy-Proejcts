# 🖤 API 통신에 대하여

## 🌟 로딩 / 에러 처리를 하는 방법에는 어떤 것들이 있을까?

### 1. (패칭 라이브러리를 사용하지 않고) 선언적으로 처리하기 - Suspense, Error Boundary

- Suspense : Suspense는 children의 로딩이 완료될 때까지 다른 컴포넌트를 대신 보여줄 수 있게 해준다. REST API와 같이 비동기로 데이터를 가져오는 작업에서 활용도가 높다.
  -> 개발자가 Loading 상태를 선언적으로 관리 가능

- ErrorBoundary : ErrorBoundary는 하위 컴포넌트 트리의 렌더링 중 발생한 에러를 감지하여 컴포넌트 트리 대신 폴백 UI를 보여줄 수 있는 컴포넌트다. 아래 컴포넌트에서 에러 발생 시 throw 하여 에러에 관한 책임을 Error Boundary가 갖도록 한다.
  -> 생명주기 메서드인 static getDerivedStateFromError() 와 componentDidCatch()를 정의하여 사용할 수 있다.
  -> 개발자가 렌더링 중 발생한 에러에 대해 선언적으로 관리 가능하다

### 2. 데이터 패칭 라이브러리 사용

- react-query, swr등 loading, error에 대한 처리를 제공해주는 패칭 라이브러리 사용하기

<br/>

## 🌟 패칭 라이브러리란 무엇이고 어떤 것들이 있을까?

### 패칭 라이브러리란?

데이터 패칭이란 웹 에플리케이션에서 서버로부터 데이터를 가져오는 기능을 뜻한다. 이러한 작업을 단순화하고 관리를 유용하게 하기 위해 사용하는 것이 데이터 패칭 라이브러리이다.

주로 웹 API를 통해 데이터를 요청하고, 응답을 받아와서 애플리케이션의 상태를 업데이트하는 작업을 처리한다.
또한 데이터 패칭 라이브러리는 HTTP 요청, 상태 관리, 오류 처리 등 다양한 기능을 제공하여 데이터를 효율적으로 관리하고 렌더링을 업데이트 하는 데 도움을 준다.

상태관리 라이브러리와의 차이점은, 상태 관리 라이브러리는 client 측의 상태를 관리하고, 데이터 패칭 라이브러리는 server 측의 데이터 정보를 관리한다는 점이 있다.

### 패칭 라이브러리의 종류

- react와 함께 사용 가능한 데이터 패칭 라이브러리는 axios, swr, tanstack query(전 react-query), Redux Toolkit Query, Apollo Client가 있다.
- 주로 인기있는 라이브러리는 tanstack query와 swr이다.

### react Query vs Swr

#### 1. React Query

📍 `React Query` : 비동기 처리를 쉽게 관리할 수 있는 라이브러리이면서 **_Client State_**에 적합한 라이브러리.

**⇒ 리액트 애플리케이션에 서버 상태를 가져오고, 캐싱/동기화/업데이트를 쉽게 하도록 한다.**

- cf. Client State vs Server State
  - `Cleint State` : 세션 간 지속되지 않는 데이터, 동기적, 클라이언트가 소유하는 데이터
    → ex. 컴포넌트의 state, 동기적으로 저장되는 redux store의 데이터
  - `Server State` : 세션간 지속되는 데이터, 비동기적, 여러 클라이언트에 의해 수정될 수 있는, 공유되는 데이터
    → ex. 비동기 요청으로 받아올 수 있는 백엔드 DB에 저장되어 있는 데이터.

**📌 특징**

- 서버에서 주기적으로 fetch 함 → 데이터를 전역에서 사용 가능
- **Optimistic Update** 기능 제공: 데이터 변경을 요청 후 실제로 요청이 성공하기 전 미리 UI만 변경 후, 서버의 응답에 따라 다시 롤백 or 업데이트 된 상태로 UI를 놔두는 것

**📌 장점**

- Redux, MobX 등의 상태관리 툴보다 **서버 상태 관리에 용이**, 직관적인 API 호출 가능
- API 처리에 대한 각종 인터페이스와 옵션 제공
  - **`useQuery`** Hook: API 데이터의 만료 시간, 리프레싱 주기, 데이터를 캐시에서 유지할 기간, 브라우저 포커스 시 데이터 리프레시 여부, 성공/에러 콜백 등 기능 제거
- Client Store : 프론트에서 정말 필요한 전역 상태만 남겨 Store 답게 사용 가능
- Devtool 제공 → 원활한 디버깅
- Cashing 전략이 필요할 때 !! 굿굿

**📌 단점**

- 컴포넌트가 상대적으로 비대해짐 (provider 필수)
- 컴포넌트 유착 최소화 및 사용처 파악 → 난이도 높아짐, 러닝커브

**📌 React Query의 상태**

1. `fresh` : 새롭게 추가 된 Query / 만료되지 않은 query
   → 컴포넌트가 mount(최초 실행) / unmount(제거) 시에도 데이터 요청 X
2. `fetching` : 요청 중인 query
3. `stale` : 요청이 만료된 query
   → 컴포넌트가 mount(최초 실행) / unmount(제거) 시에 캐싱된 데이터 요청
4. `inactive` : 비활성화 된 query
   → 특정 시간이 지나면 캐시가 가비지 컬렉터에 의해 제거된다.

**📌 useQuery hook**

- `GET` 요청과 같은 `CREATE` 작업을 할 때 사용되는 훅, 데이터를 가져온다.

```tsx
const requestData = useQuery( 쿼리 키, 쿼리 함수, 옵션);
```

- **쿼리 키** : 문자열 / 배열

  - 기본적으로 배열, 순서에 따라 다른 키로 인식

    - 키의 종류
      1. \*\*\*\*문자열: 자동으로 길이가 1인 배열로 변환 (ex. “first” → [”first”]
      2. 배열 : 문자열과 숫자가 함께 있으면 숫자로 구분
      3. promise 함수의 인자 이름 : 배열의 마지막 요소, 객체로 전달

  - 캐싱 처리에 사용
  - 유일한 키 : `fresh` 상태가 되면 키 값으로 추정

- **쿼리 함수** : Promise를 반환하는 함수
  - axios, fetch (비동기 통신 관련 라이브러리가 미리 추가 되어 있어야 함)
- **옵션** : uesQuery 기능 제어
  - 어떤걸 제어하는데?
    cacheTime, staleTime, refetchOnMount, refetchOnWindowFocus, refetchInterval, refetchIntervalInBackground, enabled, onSuccess, onError, select
- **반환 값** : Promise의 함수 status (data, isLoading, isFetching, isError, error 등)

---

#### 2. SWR

📍 `SWR(Stale-While-Revaildate)` : React Hooks library for data fetching, 데이터를 얻는 GET에 특화된 훅

**⇒ 먼저 캐시에서 데이터를 반환한 후, 서버에서 데이터를 가져오는 요청을 수행하고, 마지막에 최신 데이터를 제공한다.**

**📌 특징**

- 한 번의 fetch한 원격 상태의 데이터를 내부적으로 캐시
- 다른 컴포넌트에서 동일한 상태 사용하고자 함 → 서버 재요청X 이**전 캐시 상태 그대로 사용**
- **즉, 서로 다른 컴포넌트에서 동일한 상태 공유 가능!!**

**📌 장점**

- 전역적으로 데이터 관리 → 데이터 캐싱 기능 가능 (반복적 호출 x)
- 일정 시간마다 원격 데이터를 자동 동기화 ⇒ `revalidate`
- 수동 동기화도 가능 ⇒ `mutate`

**📌 문법**

1. 첫번째 인자 : `key`
   - 요청에 대한 유일한 식별자 역할
   - API URL을 사용하기 위해 string 또는 string을 return 하는 함수를 사용한다.
2. 두번째 인자 : `fetcher`
   - 데이터를 받아오는 함수
   - key 값(첫번째 인자)이 첫 번째 파라미터로 전달 됨
   - promise를 return 하는 모든 함수가 올 수 있음 (fetch, axios 등의 라이브러리도 가능)
   - 답이 정상적으로 이루어지면 response.data를 useSWR의 data로 사용
3. 세번째 인자 : `oprtions`
   - `refreshInterval = 0` : 기본적으로 비활성화, 설정 ms만큼 poling 주기 설정
   - `revaildateOnFocus = true` : 사용자가 페이지를 탐색하는 중 다른 탭을 보다가 다시 돌아올 때의 재 실행 여부
   - `revalidateOnReconnect = true` :네트워크가 끊겼다가 다시 연결되었을 때 재 실행 여부

**📌 반환 값**

1. `data` : 통신 전에는 undefined, 통신 후에는 응답의 결과가 저장
2. `error` : 통신 결과의 error가 저장
3. `revalidate` : 요청 또는 요청 재검증으로 통해 SWR에 정의된 API를 다시 요청하여 데이터를 검증
4. `mutate(data, shouldRevaildate)` : 캐시되는 데이터를 저장함
   - mutate(data)를 설정하면 data의 값이 변경됨
   - shouldRevaildate는 검증의 여부를 true, false로 받음

<br/>

## 🌟 패칭 라이브러리를 쓰는 이유는 무엇일까?

1. 성능과 효율성의 필요성 증가
   -> 상태 관리 및 업데이트 용이
   -> 간단한 비동기 데이터 요청

2. 최적화 된 데이터 요청 필요
   -> 캐싱과 최적화
