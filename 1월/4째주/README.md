# 질문

웹 애플리케이션에서 First Contentful Paint(FCP)와 Time to Interactive(TTI)의 차이를 설명하고, 이 두 성능 지표를 최적화하기 위한 방법들을 설명해주세요

## 1. FCP (First Contentful Paint)

- 통상적인 의미 : 페이지가 로딩되기 시작한 시점부터 화면에 의미 있는 콘텐츠(텍스트, 이미지, svg 요소, 흰색이 아닌 canvas 요소)가 처음으로 렌더링되는 시점까지의 시간 (최적은 1.8초 이내)
- 내가 이해한 의미 : 화면에 맨 처음 그려지는 컨텐츠(텍스트, 이미지 등)이 그려지는 데 걸리는 시간 (최적은 몇 초이내)

<aside>
🏃🏻‍♀️

**페이지 로딩, 로드**와 **렌더링**

- 페이지 로딩 : 사용자가 페이지로 진입한 시점
- 로드 : 페이지 로드는 페이지를 구성하는데 필요한 모든 리소스(HTML 문서와 CSS, Javascript, 이미지 등의 외부 리소스)들이 다운로드 완료되었을 때

- 렌더링 : 브라우저가 HTML, CSS, Javascript를 화면에 표시하는 과정

</aside>

### 1-1. FCP 성능 최적화

## 2. TTI (Time to Interactive)

: 웹 페이지가 완전히 ‘사용 가능한 상태’가 되는 시간

_예) 유투브 썸네일이 확인은 되지만 아직 클릭이 안된다면 아직 TTI가 완료되지 않은 시간이며,_

_썸네일을 클릭할 수 있는 시점이 TTI라고 봄_

### 2-1. 왜 바로 영상을 클릭할 수 없는건가?

웹 페이지가 단계적으로 로딩되기 때문이다.

<aside>
🏃🏻‍♀️

\*\*웹 페이지의 단계적 로딩

1. 기본적인 HTML 구조가 로드 (뼈대)
2. 그 다음 이미지와 스타일이 로드 (겉모습)
3. 마지막으로 Javascript가 로드되고 실행 (기능)\*\*

</aside>

## 3. FCP와 TTI의 차이점

FCP는 처음 화면에 그려지는 컨텐츠가 그려지는데 걸리는 시간이고,

TTI는 화면에 그려진 컨텐츠가 사용자가 사용할 수 있는 상태가 되는데 걸리는 시간이다.

즉, FCP의 측정 기준은 화면에 컨텐츠가 그려지만 하면 되지만 TTI의 측정 기준은 화면에 그려진 것 뿐만 아니라 그 컨텐츠를 사용할 수 있어야 한다.

따라서 TTI는 FCP보다 같거나 크다.

## 4. 성능 최적화

### 4-1. FCP 성능 최적화

1. SSR
   1. 서버에서 완성된 페이지를 브라우저에서 보옂
2. 이미지 최적화 : 용량 압축
3. 코드 스플리팅 : 리액트의 컴포넌트 구조가 코드를 논리적으로 분리하긴 하지만, 기본적으로 모든 컴포넌트 코드는 하나의 번들로 묶여서 전송됨

   따라서, 헤더나 내비게이션과 같이 가장 먼저 보여지는 중요한 컴포넌트들을 제외하고는 필요한 시점에 코드를 로드하게 함으로써 초기 렌더링 속도를 높여줌

   ```jsx
   // 관리자 대시보드 앱의 구조라고 가정
   import React, { lazy, Suspense } from 'react';
   import { Routes, Route } from 'react-router-dom';

   // 기본 번들에 포함되는 컴포넌트
   import Navbar from './components/Navbar';
   import Loading from './components/Loading';

   // 코드 스플리팅 적용 - 필요할 때만 로드
   const Dashboard = lazy(() => import('./pages/Dashboard'));
   const UserManagement = lazy(() => import('./pages/UserManagement'));
   const Analytics = lazy(() => import('./pages/Analytics'));
   const Reports = lazy(() => import('./pages/Reports')); // 무거운 차트 라이브러리 포함

   function App() {
     return (
       <>
         <Navbar />
         <Suspense fallback={<Loading />}>
           <Routes>
             <Route path="/" element={<Dashboard />} />
             {/* /users 접속할 때만 UserManagement 코드 로드 */}
             <Route path="/users" element={<UserManagement />} />
             {/* /analytics 접속할 때만 차트 라이브러리 포함된 코드 로드 */}
             <Route path="/analytics" element={<Analytics />} />
             <Route path="/reports" element={<Reports />} />
           </Routes>
         </Suspense>
       </>
     );
   }
   ```

### 4-2. TTI 최적화
