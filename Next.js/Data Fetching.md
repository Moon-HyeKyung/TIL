# Data Fetching

## getStaticProps(정적 생성)

page에서 호출 된 'getStaticProps'라 불리는 'async'함수를  내보내면 Next.js는 return된 props(컴포넌트)를 사용해 **build 시 해당 page를 미리 rendering**한다.

```javascript
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

### *'context'* 매개 변수는 다음 key들을 포함하는 객체

  - **'params'**

    - path parameter(동적 경로를 사용하는 페이지를 위함)
    - ex: [id].js   -> {id: "..."}

    > https://nextjs.org/docs/routing/dynamic-routes

  - **'preview'**

    - 'true'  - 페이지 미리보기 모드가 **있는** 경우
    - 'undefined' -  페이지 미리보기 모드가 **없는** 경우

    > https://nextjs.org/docs/advanced-features/preview-mode

  - **'previewData'**: 'setPreviewData'에 의해 설정된 미리보기 데이터

    > https://nextjs.org/docs/advanced-features/preview-mode

  - **'locale'**: 현재 active locale(if enabled)

  - **'locales'**: 모든(다국) supported locale(if enabled)

  - **'defaultLocale'**: 설정된 default locale(if enabled)

<br/><br/>

### *'getStaticProps'* 는 다음과 함께 object를 return해야 함

  - **'props'**: required(object)

    -  page componet가 받을 요소(props)가 있는 필수 개체
    - serializable object여야 함

  - **'revalidate'**: optional(number)

    - page re-generate가 일어날 수 있는 시간(초 단위)
    - ex: 1초로 적용 -> 요청이 들어올 때 1초마다 page를 re-generation
    - 새로운 post가 생성되더라도 재 build, 재 deploy 하는 것 없이 사용될 수 있음

  - **'notFound'**: optional(boolean)

    - 404 page로 return

    - **getStaticPaths에서 return된 경로**만 미리 렌더링

    - **fallback: false일 경우 notFound는 필요 x**

    - ```javascript
      export async function getStaticProps(context) {
        const res = await fetch(`https://.../data`)
        const data = await res.json()
      
        if (!data) {
          return {
            notFound: true,
          }
        }
      
        return {
          props: { data }, // will be passed to the page component as props
        }
      }
      ```

  - **'redirect'**: optional({destination: string, permanent: boolean})

    - 내부나 외부 resource로 redirect

      ```javascript
      export async function getStaticProps(context) {
        const res = await fetch(`https://...`)
        const data = await res.json()
      
        if (!data) {
          return {
            redirect: {
              destination: '/',
              permanent: false,
            },
          }
        }
      
        return {
          props: { data }, // will be passed to the page component as props
        }
      }
      ```

      > build 시에 redirecting은 현재 허용되지 않는다.   
      > build 시에 redirect를 허용 ->  `next.config.js`에 다음과 같이 추가 해야 된다.
      >
      > ```js
      >  module.exports = {
      >    async redirects() {
      >      return [
      >        {
      >          source: '/about',
      >          destination: '/',
      >          permanent: true,
      >        },
      >      ]
      >    },
      >  }
      > ```

- top-level scope module 사용 가능

  - import된 module은 client 측에 bundle로 제공되지 않음

  - 서버 측 코드 작성 가능

  - filesystem, database 읽는 것 포함

    > top-level scope module
    >
    > ```javascript
    > import { something } from "<module>"; // <-- module == Global / Tope level scope
    > ```

- **Application 내**에 API route를 호출하기 위한 fetch() 사용 불가

  - 대신 API route 내에서 사용되는 로직(control)을 직접 가져와야 함

  - 접근 방식을 위해 code를 약간 refactoring해야 할 수 있음

  - **외부 API**는 가능

    > https://stackoverflow.com/questions/64040992/how-to-import-api-route-in-nextjs-for-getstaticprops

  - Ex code

    ```javascript
    // posts will be populated at build time by getStaticProps()
    function Blog({ posts }) {
      return (
        <ul>
          {posts.map((post) => (
            <li>{post.title}</li>
          ))}
        </ul>
      )
    }
    
    // This function gets called at build time on server-side.
    // It won't be called on client-side, so you can even do
    // direct database queries. See the "Technical details" section.
    export async function getStaticProps() {
      // Call an external API endpoint to get posts.
      // You can use any data fetching library
      const res = await fetch('https://.../posts')
      const posts = await res.json()
    
      // By returning { props: { posts } }, the Blog component
      // will receive `posts` as a prop at build time
      return {
        props: {
          posts,
        },
      }
    }
    
    export default Blog
    ```

    

- **언제 `getStaticProps`를 사용해야 하나요?**

  - client요청에 앞서서 build 시에 페이지를 pre-render에 이용할 수 있는 데이터가 있을 때

  - headless CMS로부터 오는 데이터가 있을 때

    > https://simsimjae.medium.com/headless-cms%EB%9E%80-49569dc86daa

  - cached될 수 있는 데이터가 있을 때(사용자 별로 말고).

  - SEO(Search Engine Optimization)를 위해 pre-rendering 되어야만 하는 페이지일 때

- TypeScript 에서의 사용

  - 'getStaticProps는' GetStaticProps'를 이용해 사용

  ```javascript
  import { GetStaticProps } from 'next'
  
  export const getStaticProps: GetStaticProps = async (context) => {
    // ...
  }
  ```

  - type 검증을 원하면 'InferGetStaticPropsType<typeof getStaticProps>'를 이용

    ```javascript
    import { InferGetStaticPropsType } from 'next'
    
    type Post = {
      author: string
      content: string
    }
    
    export const getStaticProps = async () => {
      const res = await fetch('https://.../posts')
      const posts: Post[] = await res.json()
    
      return {
        props: {
          posts,
        },
      }
    }
    
    function Blog({ posts }: InferGetStaticPropsType<typeof getStaticProps>) {
      // will resolve posts to type Post[]
    }
    
    export default Blog
    ```
<br/><br/>

### Incremental Static Regeneration(증분 정적 재생성)

<br/><br/><br/>



## getServerSideProps (서버 측 렌더링)
