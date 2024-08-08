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
