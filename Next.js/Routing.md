# Next.js의 페이지

1. Next.js에서 페이지는 pages디렉토리 의 파일에서 내 보낸 React Component이다.

2. 페이지는 파일 이름 에 따라 경로와 연결된다.

   - pages/index.js는 root(http://localhost:3000) 경로와 연결.   
   - pages/posts/first-post.js는 /posts/first-post경로와 연결.

   <br/>

   > /pages/posts/firstpost.tsx

   ```typescript
   export default function FirstPost() {
     return <h1>First Post</h1>
   } 
   ```
   - component는 모든 이름을 가질 수 있지만 'export default'로 내 보내야한다.

   - 이제 개발 서버가 실행 중인지 확인하고($ npm run dev)
      [http : // localhost : 3000 / posts / first-post](http://localhost:3000/posts/first-post)로 들어가서 확인


3. pages디렉토리 아래에 JS 파일을 생성하기 만하면 파일 경로가 URL 경로

# 동적 라우팅
> /pages/posts/[id].tsx
