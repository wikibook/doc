# 코드 색상 적용

160쪽

~~~typescript
import React, { ErrorInfo, PropsWithChildren } from "react";

type Props = PropsWithChildren<{}>;
type State = { hasError: boolean; errorMessage: string };

export default class ErrorBoundary extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = {
      hasError: false,
      errorMessage: "",
    };
  }

  static getDerivedStateFromError(error: Error) {
    return {
      hasError: true,
      errorMessage: error.toString(),
    };
  }

  // componentDidCatch를 추가했다.
  componentDidCatch(error: Error, info: ErrorInfo) {
    console.log(error);
    console.log(info);
  }

  render() {
    // 에러가 발생했을 경우에 렌더링할 JSX
    if (this.state.hasError) {
      return (
        <div>
          <h1>에러가 발생했습니다.</h1>
          <p>{this.state.errorMessage}</p>
        </div>
      );
    }
    // 일반적인 상황의 JSX
    return this.props.children;
  }
}
~~~


162쪽

~~~jsx
function App() {
  return (
    <ErrorBoundary name="parent">
      {/* Child에서 발생한 에러는 여기에서 잡힌다. */}
      <ErrorBoundary name="child">
        <Child />
      </ErrorBoundary>
    </ErrorBoundary>
  )
}
~~~

376쪽
~~~typescript
function RecoilRoot(props: Props): React.Node {
  const { override, ...propsExceptOverride } = props

  const ancestorStoreRef = useStoreRef()
  if (override === false && ancestorStoreRef.current !== defaultStore) {
    // If ancestorStoreRef.current !== defaultStore, it means that this
    // RecoilRoot is not nested within another.
    return props.children
  }

  return <RecoilRoot_INTERNAL {...propsExceptOverride} />
}
~~~

376쪽

~~~typescript
const AppContext = React.createContext<StoreRef>({ current: defaultStore })
const useStoreRef = (): StoreRef => useContext(AppContext)
~~~

709쪽

~~~typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'

const rootElement = document.getElementById('root')
const root = ReactDOM.createRoot(rootElement)

root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
~~~

710쪽

~~~typescript
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'

const root = createRoot(document.getElementById('root'))

root.render(
  <StrictMode>
    <App />
  </StrictMode>,
)
~~~


715쪽

~~~typescript
export default function App() {
  console.log('component body')

  const [number, setNumber] = useState(() => {
    console.log('initialize state')
    return 0
  })

  const handleClick = useCallback(() => {
    setNumber((prev) => prev + 1)
  }, [])

  const tenTimes = useMemo(() => {
    console.log('* 10!')
    return number * 10
  }, [number])

  return <button onClick={handleClick}>{tenTimes} 클릭</button>
}
~~~

719쪽

~~~typescript
function ProfilePage() {
  // initialResource는 Promise로 데이터를 불러오는 데 어느 정도 시간이 필요함
  const [resource, setResource] = useState(initialResource)
  return (
    <>
      <Suspense
        fallback={
          <>
            <h1>Loading profile...</h1>
          </>
        }
      >
        <Suspense fallback={<h1>Loading posts...</h1>}>
          <Sibling name="two" />
          <ProfileTimeline resource={resource} />
        </Suspense>
      </Suspense>
    </>
  )
}
~~~

719쪽

~~~typescript
// components/SampleComponent.tsx
export default function SampleCompnent() {
  return <>동적으로 가져오는 컴포넌트</>
}

// pages/index.tsx
import { Suspense, lazy } from 'react'

const DynamicSampleComponent = lazy(
  () => import('../components/SampleComponent'),
)

export default function Home() {
  return (
    <Suspense fallback={<>로딩중</>}>
     <DynamicSampleComponent />
    </Suspense>
  )
}
~~~

720쪽

~~~typescript
import { Suspense, useEffect, useState, ComponentProps } from 'react'

function useMounted() {
  const [mounted, setMounted] = useState(false)

  // useEffect는 클라이언트에서만 실행되므로 mounted가 true면
  // 클라이언트에서 실행되는 코드다.
  useEffect(() => {
    setMounted(true)
  }, [])
  
  return mounted
}

export default function CustomSuspense(
  props: ComponentProps<typeof Suspense>,
) {
  const isMounted = useMounted()

  if (isMounted) {
    return <Suspense {...props} />
  }
  return <>{props.fallback}</>
}
~~~

734쪽

~~~typescript
export default function NotFound() {
  return (
    <>
      <h2>Not Found</h2>
      <p>404</p>
    </>
  )
}
~~~

734쪽

~~~typescript
export default function Loading() {
  return 'Loading...'
}
~~~

760쪽

~~~ts
async function serverAction() {
  "use server";
  // 서버에 바로 접근하는 코드
}

// 이 파일 내부의 모든 내용이 서버 액션으로 간주된다.
'use server'

export async function myAction() {
  // ...
  // 서버에 바로 접근하는 코드
}
~~~

761쪽

~~~ts
export default function Page() {
  async function handleSubmit() {
    'use server'

    console.log('해당 작업은 서버에서 수행합니다. 따라서 CORS 이슈가 없습니다.')

    const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'post',
      body: JSON.stringify({
        title: 'foo',
        body: 'bar',
        userId: 1,
      }),
      headers: {
        'Content-type': 'application/json; charset=UTF-8',
      },
    })
  
    const result = await response.json()
    console.log(result)
  }

  return (
    <form action={handleSubmit}>
      <button type="submit">form 요청 보내보기</button>
    </form>
  )
}
~~~

762쪽

~~~ts
// .next/server/app/server-action/form/page.js

// 해당 페이지에서 수행하는 서버 액션을 모아둔다.
const actions = {
  // 앞서 페이로드에서 본 액션 아이디를 확인할 수 있다.
  d0b22c18e02d4e94b1ed17222f5f945a78603861: () =>
    Promise.resolve(/* import() eager */)
      .then(__webpack_require__.bind(__webpack_require__, 5948))
      .then((mod) => mod['$$ACTION_0']),
}

// ...

// 해당 페이지
function Page() {
  async function handleSubmit() {
    return $$ACTION_0(handleSubmit.$$bound)
  }
  // ...
}

//...

async function $$ACTION_0(closure) {
  console.log('해당 작업은 서버에서 수행합니다. 따라서 CORS 이슈가 없습니다.')
  const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
    method: 'post',
    body: JSON.stringify({
      title: 'foo',
      body: 'bar',
      userId: 1,
    }),
    headers: {
      'Content-type': 'application/json; charset=UTF-8',
    },
  })
  const result = await response.json()
  console.log(result)
}
~~~


768쪽

~~~ts
export default function Page() {
  async function handleClick() {
    'use server'

    // server mutation이 필요 없는 작업
  }
  return <button onClick={handleClick}>form 요청 보내보기</button>
}
~~~


770쪽

~~~ts
import { ReactNode } from 'react'

import { fetchPostById } from '#services/server'

export default async function Page({
  params,
}: {
  params: { id: string }
  children?: ReactNode
}) {

  // const response = await fetch(
  //  `https://jsonplaceholder.typicode.com/posts/${id}`,
  //  options,
  // )
  // const data = await response.json()
  // 와 같다.
  const data = await fetchPostById(params.id, { cache: 'no-cache' })

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.body}</p>
    </div>
  )
}
~~~

853쪽

~~~html
<div class="cont_visual" style="background-size: cover; background-color: rgb(59, 56. 32); background-image: url("http...);">
~~~
