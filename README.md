# @tanstack/react-query (v5.51.1)

react-query는 react application에서 서버 상태 페칭, 캐싱, 동기화 및 업데이트를 보다 쉽게 다룰 수 있도록 도와주는 라이브러리입니다.

## QueryClient & QueryClientProvider

QueryClient는 애플리케이션 내 유일해야하며 QueryClient를 통해 캐시와 상호작용을 할 수 있으며 모든 query와 mutation에 기본 옵션을 설정할 수 있습니다.

QueryClient는 생성자로 new 연산자와 함께 호출해야 하며, 호출할 때 defaultOptions 프로퍼티에 쿼리에 대한 옵션을 설정한 객체를 인수로 전달할 수 있습니다.

QueryClientProvider 컴포넌트를 최상단에 위치시키고 client props에 생성한 QueryClient 인스턴스를 전달해줍니다.

```javascript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

export default function App() {
  const queryClient = new QueryClient({
    defaultOptions: {
      // 모든 쿼리에 공통적으로 적용되는 옵션을 설정할 수 있습니다.
      queries: {
        // 쿼리를 처음 실행할 때 사용되는 초기 데이터입니다.
        initialData: undefined,

        // 쿼리가 데이터를 페칭하는 동안 컴포넌트에 제공될 임시 데이터를 설정합니다.
        placeholderData: undefined,

        // 초기 데이터가 마지막으로 업데이트된 시간을 타임스탬프(밀리초)로 설정합니다.
        initialDataUpdatedAt: undefined,

        // 쿼리와 관련된 추가 정보를 전달할 수 있습니다.
        // meta는 쿼리에 접근할 수 있는 모든 곳에서 사용할 수 있습니다.
        meta: undefined,

        // 쿼리의 요청 방식을 설정하는 옵션입니다.
        // online: 요청을 우선적으로 수행하고 캐시된 데이터를 필요시에 사용합니다.
        // offlineFirst: 캐시된 데이터를 우선적으로 사용하여 요청 수행을 최소화합니다.
        // always: 캐시된 데이터가 있더라도 항상 요청을 수행합니다.
        networkMode: 'online',

        // 컴포넌트의 리렌더링을 제어할 수 있습니다.
        notifyOnChangeProps: 'all',

        // 쿼리 활성화 여부를 설정합니다.
        enabled: true,

        // 쿼리 데이터가 fresh 상태에서 stale 상태로 변경되기까지의 시간을 설정합니다.
        staleTime: 0,

        // inactive 상태인 쿼리 데이터가 가비지 컬렉팅되기까지의 시간을 설정합니다.
        gcTime: 30000,

        // 쿼리 실패시 재시도 횟수를 설정합니다.
        retry: 3,

        // retry 사이의 대기 시간을 설정합니다.
        // 첫 번째 인수로는 실패 횟수, 두 번재 인수로는 에러 객체를 전달받습니다.
        retryDelay: (failureCount, error) => Math.min(1000 * 2 ** failureCount, 30000),

        // 컴포넌트가 마운트될 때 실패한 쿼리를 다시 시도할지 여부를 설정합니다.
        retryOnMount: true,

        // 쿼리의 데이터를 일정한 간격으로 refetching할 수 있도록 설정합니다.
        refetchInterval: false,

        // 쿼리가 백그라운드 상테에서도 일정한 간격으로 refetching할 수 있도록 설정합니다.
        refetchIntervalInBackground: false,

        // 컴포넌트가 마운트될 때 쿼리의 데이터를 자동으로 fetching할 지 여부를 설정합니다.
        refetchOnMount: true,

        // 네트워크가 다시 연결될 때 쿼리의 데이터를 자동으로 fetching할 지 여부를 설정합니다.
        refetchOnReconnect: true,

        // 브라우저에 다시 포커싱되었을 때 쿼리의 데이터를 자동으로 fetching할 지 여부를 설정합니다.
        refetchOnWindowFocus: true,

        // 쿼리의 데이터를 가공할 때 사용하는 옵션으로 쿼리 원본 데이터를 수정하거나 필터링할 때 사용됩니다.
        // 인수로 원본 데이터를 전달받으며 반환값이 최종적으로 반환되는 data에 바인딩됩니다.
        select: undefined,

        // 기존 데이터와 새로운 데이터 간의 차이점을 효율적으로 처리하기 위해 사용되는 옵션입니다.
        // true: 변경된 부분만 업데이트하고 나머지 데이터는 재사용합니다.
        // false: 데이터가 변경될 때마다 전체 데이터 구조를 새로 생성합니다.
        structuralSharing: true,

        // 쿼리에서 발생한 에러를 컴포넌트 또는 상위 컨텍스트에게 전달되도록 설정하는 옵셥입니다.
        throwOnError: false,

        // 쿼리의 기본 동작 방식을 정의할 수 있는 옵션입니다.
        behavior: {
          // queryFn이 실행되기 전에 실행되는 메서드입니다.
          onFetch(context) {
            // useQuery에 지정한 queryFn과 동일한 함수이며 쿼리 함수에 직접 접근하고 실행할 수 있습니다.
            context.fetchFn();

            // refetch 요청을 취소할 수 있는 옵션이며 false로 설정시 refetch를 막을 수 있습니다.
            context.fetchOptions?.cancelRefetch;
            // 쿼리가 초기 상태일 때 사용하는 프로미스 객체를 설정할 수 있습니다.
            context.fetchOptions?.initialPromise;
            // 페이지네이션이나 무한 스크롤 시 더 많은 데이터를 가져올 때 방향('forward', 'backward')을 설정할 수 있습니다.
            context.fetchOptions?.meta?.fetchMore?.direction;

            // 쿼리를 생성할 때 사용된 옵션들을 설정할 수 있습니다.
            context.options;

            // useQuery에서 지정한 queryKey 값을 갖고 있습니다.
            context.queryKey;

            // AbortSignal를 사용하여 fetch 요청을 취소할 수 있도록 도와주는 AbortSignal 객체가 바인딩되어 있습니다.
            context.signal;

            // 쿼리의 현재 상태 정보를 제공합니다.
            context.state;
          }
        }
      },
      // 모든 뮤테이션에 공통적으로 적용되는 옵션을 설정할 수 있습니다.
      mutations: {
        scope: {
          // 병렬로 실행되는 여러 mutationFn 함수중 id로 작성된 mutaitonFn들은 직렬로 실행되도록 설정합니다.
          id: ''
        },

        // mutationFn 성공시 실행되는 메서드입니다.
        onSuccess: undefined,

        // mutationFn 실패시 실행되는 메서드입니다.
        onError: undefined,

        // mutationFn 성공 혹은 실패시 실행되는 메서드입니다.
        onSettled: undefined
      },
      // SSR이나 SSG 환경에서 dehydrate시 쿼리 상태 처리를 제어하는 데 사용됩니다.
      dehydrate: {
        // 데이터의 직렬화 방식을 정의하는 함수를 작성합니다.
        serializeData: undefined,

        // 특정 쿼리를 직렬화할지 여부를 결정하는 함수를 작성합니다.
        shouldDehydrateQuery: () => true,

        // 특정 뮤테이션을 직렬화할지 여부를 결정하는 함수를 작성합니다.
        shouldDehydrateMutation: () => false
      },
      // SSR이나 SSG 환경에서 hydrate시 쿼리 상태 처리를 제어하는 데 사용됩니다.
      hydrate: {
        // 데이터의 역직렬화 방식을 정의하는 함수를 작성합니다.
        deserializeData: undefined,

        // MutationOptions을 작성합니다.
        mutations: {
          // ...MutationOptions
        },

        // QueryOptions을 작성합니다.
        queries: {
          // ...QueryOptions
        }
      }
    }
  });

  // QueryClientProvider를 최상단에서 감싸주고 QueryClient 인스턴스를 client props로 넣어 애플리케이션에 연결해야 한다.
  return <QueryClientProvider client={queryClient}>,,,</QueryClientProvider>;
}
```

### Using App Router

#### QueryCLientProvider Component

QueryClientProvider 컴포넌트는 Context API를 사용하기 때문에 서버 컴포넌트에 사용할 수 없으므로 클라이언트 컴포넌트로 분리하여 사용해야 합니다.

```javascript
// ReactQueryProviders.tsx
'use client';

import { useState, PropsWithChildren } from 'react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

export default function ReactQueryProviders({ children }: PropsWithChildren) {
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            // 클라이언트측에서 즉시 refetching되는 것을 방지하기 위해서 staleTime 값을 0으로 사용되즌 것을 방지
            staleTime: 60 * 1000
          }
        }
      })
  );

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>>
  )
}
```

```javascript
// app/layout.tsx
import { PropsWithChildren } from 'react';

import ReactQueryProviders from '@/shared/components/ReactQueryProviders';

export default function RootLayout({ children }: PropsWithChildren) {
  return (
    <html>
      <head />
      <body>
        <ReactQueryProviders>{children}</ReactQueryProviders>
      </body>
    </html>
  );
}
```

#### prefetching & de/hydrate

서버에서 prefeth한 쿼리들을 갖는 queryClient를 dehydrate시켰다가 이후 클라이언트측에 전달할 때는 hydrate 시켜 전달해주어야 합니다.

```javascript
// getDehydratedQuery.ts

import {
  QueryClient,
  dehydrate,
  QueryState,
  QueryKey
} from '@tanstack/react-query';
import { cache } from 'react';

import { isEqual } from '@/utils';

// cache 함수를 사용하여 QueryClient를 매번 생성하지 않고 캐싱된 queryClient를 재사용
export const getQueryClient = cache(() => new QueryClient());

type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

interface QueryProps<ResponseType = unknown> {
  queryKey: QueryKey;
  queryFn: () => Promise<ResponseType>;
}

interface DehydratedQueryExtended<TData = unknown, TError = unknown> {
  state: QueryState<TData, TError>;
}

export async function getDehydratedQuery<Q extends QueryProps>({
  queryKey,
  queryFn,
}: Q) {
  const queryClient = getQueryClient();

  // 필요한 쿼리를 prefetching
  await queryClient.prefetchQuery({ queryKey, queryFn });

  // perfetching한 쿼리를 갖고 있는 queryClient를 dehydate
  // dehydate 메서드는 쿼리들을 직렬화시키고 이를 이용하여 per-rendering
  const { queries } = dehydrate(queryClient);

  // dehydate 함수는 prefetcing한 모든 데이터들을 반환하기 때문에 요청한 쿼리만을 필터링하여 반환
  const [dehydratedQuery] = queries.filter((query) =>
    isEqual(query.queryKey, queryKey)
  );

  return dehydratedQuery as DehydratedQueryExtended<
    UnwrapPromise<ReturnType<Q['queryFn']>>
  >;
}
```

```javascript
// app/page.tsx

import { HydrationBoundary } from 'react-query';

import { getDehydratedQuery } from '@/app/shared/utils/getDehydratedQuery';

export default async function Home({ children }: PropsWithChildren) {
  const { queryKey, queryFn } = queryOptions.all();

  const query = await getDehydratedQuery({ queryKey, queryFn });

  return (
    <main>
      {/* HydrationBoundary 컴포넌트는 클라이언트측에서 dehydrated된 쿼리들을 역직렬화 시켜 hydration 과정에서 초기 쿼리들로 사용 */}
      <HydrationBoundary state={{ queries: [query] }}>{children}</HydrationBoundary>
    </main>
  );
}
```

## Caching Life Cycle

- fresh

query에 설정된 staleTime이 지나지 않은 상태이며, fresh 상태에서는 refetching하지 않으며 캐싱된 쿼리를 사용합니다.

- fetching

queryFn이 실행중인 상태입니다.

- stale

query에 설정된 staleTime이 지났으며 이는 refetching하여 새로운 데이터를 불러 캐싱하게 됩니다. 이때 refetching하는 동안에는 기존 stale 상태의 쿼리르 사용하며 이후 refething이 완료되면 새로운 데이터로 교체하고 이를 캐싱하게 됩니다.

- inactive

쿼리가 실행된 컴포넌트가 unmount되는 경우 해당 쿼리는 inactive 상태가 됩니다. inactive 상태는 사용되지는 않지만 메모리상 아직 캐싱되어 있는 상태입니다.

- delete

inactive 상태인 쿼리에 설정된 gcTime이 지난 상태이며 이는 가비지 컬렉터에 의해 메모리상에서 제거됩니다.

## useQuery

```javascript
import { useQuery } from 'react-query';

async function fetchData() {
  return fetch('https://,,,', { method: 'GET' });
}

const {
  data, // queryFn이 반환한 값, 초기 queryFn 실행이 완료되기 전에는 undefined 반환
  dataUpdatedAt, // 마지막으로 fetching한 타임스탬프
  error, // queryFn 실행 중 에러 발생한 경우 에러 객체가 바인딩
  isError, // queryFn 실행 중 에러 발생 여부
  errorUpdateCount, // 에러가 발샣하기 까지의 queryFn 실행 횟수
  errorUpdatedAt, // 마지막으로 에러가 발생한 타임스탬프
  failureCount, // 에러가 발생한 횟수
  failureReason, // 마지막으로 발생한 에러의 메세지 혹은 객체
  fetchStatus, // 햔제 queryFn 실행 상태 반환(idle, fetching, pause)
  isFetched, // queryFn이 성공적으로 실행되었는지 여부
  isFetchedAfterMount, // 마운트된 이후 queryFn이 성공적으로 실행되었는지 여부
  isFetching, // queryFn이 현재 실행중인지 여부
  isLoading, // queryFn이 첫 실행이며 현재 실행중인지 여부
  isLoadingError, // queryFn 첫 실행중 에러 발생 여부
  isPaused, // queryFn 일시 중지 여부
  isPending, // queryFn 실행 대기중 여부(isFetching과 유사하지만 더 포괄적)
  isPlaceholderData, // 현재 data가 placeholder 데이터인지 여부
  isRefetchError, // refetching 도중 에러 발생 여부
  isRefetching, // 현재 refetching 중인지 여부
  isStale, // 캐싱 상태가 stale인지 여부
  isSuccess, // queryFn이 성공적으로 실행되었는지 여부
  refetch, // 수동으로 refetching하는 함수
  status // 현재 쿼리 상태를 반환(idle, loading, error, success)
} = useQuery({
  /**
   * queryKey를 기반으로 캐싱 관리되며 만약 queryFn 함수 내에서 특정 변수에
   * 의존하고 있다면 queryKey 배열 요소로 추가해주어야 함.
   * **/
  queryKey: ['queryKey'],
  queryFn: fetchData // Promise 객체를 반환하는 함수 작성
});
```

## useQueries

```javascript
import { useQueries } from 'react-query';

async function fetchData1() {
  return fetch('https://,,,', { method: 'GET' });
}

async function fetchData2() {
  return fetch('https://,,,', { method: 'GET' });
}

/**
 * useQueries 훅은 여러 쿼리들을 병렬로 요청하고
 * 모든 쿼리 결과가 포함된 배열을 반환
 * **/
const queryResults = useQueries({
  queries: [
    {
      queryKey: ['fetchData1'],
      queryFn: fetchData1
    },
    {
      queryKey: ['fetchData2'],
      queryFn: fetchData2
    }
  ]
});
```

## useSuspenseQuery

useSuspenseQuery 훅은 Suspense와 함께 사용하면 쿼리 상태(status)가 loading인 경우 Suspense의 fallback props에 설정한 컴포넌트를 표시해줍니다.

```javascript
import { useSuspenseQuery } from 'react-query';

export default function Child() {
  async function fetchData() {
    return fetch('https://,,,', { method: 'GET' });
  }

  const queryResult = useSuspenseQuery({
    queryKey: ['queryKey'],
    queryFn: fetchData
  });

  return <div>{/* ,,, */}</div>;
}
```

```javascript
import { Suspense } from 'react';

export default function Parent() {
  return (
    <Suspense fallback={<div>loading,,,</div>}>
      <Child />
    </Suspense>
  );
}
```

## useInfiniteQuery

useInfiniteQuery 훅은 특정 조건에서 추가적인 데이터를 fetching 기능을 제공해줍니다. 이를 통해 무한 스크롤 이나 페이징 기능을 구현할 수 있습니다.

```javascript
import { useInfiniteQuery } from '@tanstack/react-query';

const {
  data: {
    pages, // 모든 페이지의 데이터를 포함하는 배열
    pageParams // 모든 페이지에 대한 pageParam 배열
  },
  fetchNextPage, // 다음 페이지의 데이터를 fetching하는 함수이며, 호출시 queryFn이 실행
  fetchPreviousPage, // 이전 페이지의 데이터를 fethcing하는 함수
  hasNextPage, // 다음 페이지 존재 여부를 반환. 해당 값은 getNextPageParam 함수가 undefined인 경우 true 반환
  hasPreviousPage, // 이전 페이지의 존재 여부 반환. getPreviousPageParam 함수가 undefined인 경우 true 반환
  isFetchingNextPage, // 다음 페이지의 데이터를 fetching 중인지 여부를 반환
  isFetchingPreviousPage, // 이전 페이지의 데이터를 fetching 중인지 여부를 반환
  ...result
} = useInfiniteQuery({
  queryKey, // 쿼리를 고유하게 식별하는 키
  queryFn: ({ pageParam }) => fetchPage(pageParam), // 데이터를 fetching하는 비동기 함수, 인수로 pageParam 전달받아 실행
  initialPageParam: 1, // 초기 pageParam 값
  getNextPageParam: (lastPage, allPages, lastPageParam, allPageParams) => lastPage.nextCursor, // 다음 페이지의 queryFn 함수 pageParam 값 반환. 인수로 현재 페이지의 데이터, 전체 페이지의 데이터 배열, 현재 페이지의 pageParam, 전체 페이지의 pageParam 배열을 전달받아 실행
  getPreviousPageParam: (firstPage, allPages, firstPageParam, allPageParams) => firstPage.prevCursor // 이전 페이지의 queryFn 함수 pageParam 값 반환
});
```

## useMutation

useMutation 훅은 주로 데이터 생성, 수정, 삭제와 같은 변이 작업을 처리할 때 사용합니다. useMutation을 통해 비동기 작업을 수행하고, 해당 작업의 상태를 추적할 수 있습니다.

```javascript
import { useMutation } from '@tanstack/react-query';

const {
  data, // mutationFn 실행이 완료되어 반환한 데이터
  error, // mutationFn 실행 중 발생한 에러 객체
  isError, // mutationFn 실행 실패 여부를 반환
  isIdle, // mutationFn 실행 전인지에 대한 여부를 반환
  isPending, // mutationFn 실행 대기중이거나 실행중인지에 대한 여부를 반환
  isPaused, // mutationFn 실행의 중지 여부를 반환
  isSuccess, // mutationFn 실행 성공적으로 완료되었는지에 대한 여부
  failureCount, // mutationFn 실행 실패한 횟수
  failureReason, // mutationFn 마지막에 실행 실패한 에러 객체
  mutate, // mutate 메서드 호출 시 mutationFn 실행
  mutateAsync, // mutateAsync 호출 시 Promise 객체 반환하는 mutationFn 실행
  reset, // mutationFn 실행 상태 초기화 메서드(data, error, isSuccess, isError 등 초기화)
  status, // mutationFn 실행 상태(idle, pending, error, success) 반환
  submittedAt, // mutationFn 처음 실행된 타임스탬프 반환
  variables // 마지막 mutate 메서드 호출 시 전달한 변수 반환
} = useMutation({
  mutationFn, // 실제 변이 작업 수행하는 함수
  gcTime, // 변이 작업 캐시가 GC 되기까지의 시간(밀리초)
  meta, // 변이 작업에 추가적으로 전달할 메타데이터
  mutationKey, // 변이 작업을 고유하게 식별하는 키 값
  networkMode, // 네트워트 상태에 따른 변이 작업 처리 설정
  onError, // 변이 작업 실패시 호출되는 콜백 함수. error, variables, context를 인자로 전달 받아 실행
  onMutate, // 변이 작업 실행 전에 호출되는 콜백 함수
  onSettled, // 변이 작업 성공하거나 실패한 후에 항상 호출되는 콜백 함수. data, error, variables, context 인자 전달 받아 실행
  onSuccess, // 변이 작업 성공적으로 완료된 후 호출되는 콜백 함수. data, variables, context 인자 전달 받아 실행
  retry, // 변이 작업 실패했을 때 재시도할 횟수
  retryDelay, // 변이 작업 실패했을 때 재시도하기 전 대기할 시간(밀리초)
  scope, // 변이 작업 범위 지정
  throwOnError // true 설정 시 변이 작업 중 에러 발생한 경우 에러를 throw하여 호출자에게 전달
});
```
