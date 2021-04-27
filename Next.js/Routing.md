# Next.js의 페이지

1. Next.js에서 페이지는 pages디렉토리 의 파일에서 내 보낸 React Component이다.

2. 페이지는 파일 이름 에 따라 경로와 연결된다.

   - pages/index.js는 root(http://localhost:3000) 경로와 연결.   
   - pages/posts/first-post.js는 /posts/first-post경로와 연결.

   <br/>

   > /pages/posts/firstpost.tsx   
   > **js & jsx: javascript file / ts & tsx: typescript file*

   ```typescript
   export default function FirstPost() {
     return <h1>First Post</h1>
   } 
   ```
   - component는 모든 이름을 가질 수 있지만 'export default'로 내 보내야한다.

   - 이제 개발 서버가 실행 중인지 확인하고($ npm run dev)
      [http : // localhost : 3000 / posts / first-post](http://localhost:3000/posts/first-post)로 들어가서 확인


3. pages디렉토리 아래에 JS 파일을 생성하기 만하면 파일 경로가 URL 경로
<br/>
<br/>


# 동적 라우팅
1. 페이지에 대괄호( [param])를 추가 하여 동적 경로 (일명 url 슬러그, 예쁜 URL 등)를 만들 수 있다.
   - pages 폴더에 post/[pid]폴더에 /[pid].tsx를 위치하는 식으로 동적라우팅 구현 
      - 파일경로: /pages/post/[pid].tsx, URL: 도메인/post/1
      - 파일경로: /pages/post/[categori]/[pid].tsx, URL: 도메인/post/맛집/1
2. 일치하는 path parameter는 페이지에 query parameter로 전송되고 다른 query parameter와 병합된다.
   - /post/abc   
    ```javascript { "pid": "abc" }```
   - /post/abc?foo=bar   
   ```javascript { "foo": "bar", "pid": "abc" } ```
   
3. path parameter는 동일한 이름의 query parameter를 override(덮어씀)
   - /post/abc?pid=123   
   ```javascript {"pid": "abc"} ```   
4. Multiple dynamic route segments도 동일한 방식으로 작동
   - 파일경로: pages/post/[pid]/[comment].js, URL: /post/abc/a-comment   
   ```javascript { "pid": "abc", "comment": "a-comment" } ```


> /pages/post/[pid].tsx
```typescript
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```
<br/>
<br/>


## all routes
'...'을 추가하여 모든 경로를 획득해 동적 경로를 확장할 수 있다.
   - 파일경로: pages/post/[...slug].js, 경로: '/post/a', '/post/a/b', 'post/a/b/c'
      - /post/a   
         ```javascript { "slug": ["a"] } ```
      - /post/a/b   
         ```javascript { "slug": ["a","b"] } ```
         
<br/>
<br/>

## Optional among all routes
매개 변수를 이중 괄호 ( [[...slug]]) 에 포함하여 모든 경로를 선택적으로 포착 할 수 있습니다 .
