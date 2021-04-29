# 내장 CSS

## Global Stylesheet 추가
- application에 stylesheet를 추가하려면 'pages/_app.js'안에 CSS를 import 해야 함
- ex) 'style.css'를 'pages/_app.js'에 추가해 global stylesheet로 만들기
  > style.css
  ``` css
  body {
    font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
      'Arial', sans-serif;
    padding: 20px 20px 60px;
    max-width: 680px;
    margin: 0 auto;
  }
  ```
  > pages/_app.js
  ```javascript
  import '../styles.css'

  // This default export is required in a new `pages/_app.js` file.
  export default function MyApp({ Component, pageProps }) {
    return <Component {...pageProps} />
  }
  ```
- 위와 같은 과정을 거치면 모든 페이지 및 구성요소에 스타일이 적용됨
- production build 시에는 하나의 .css 파일로 minified됨
- 여기에 import한 스타일(.css)는 다른 곳에 넣지 말 것(충돌 발생)

## Style(css) 가져오기 'node_modules'
- 'bootstrap'이나 'nprogress'같은 global stylesheets같은 경우 'pages/_app.js'에 import 해야 한다.
```javascript
// pages/_app.js
import 'bootstrap/dist/css/bootstrap.css'

export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

- 타사 구성 요소에 필요한 CSS를 가져오려면 구성 요소에서 가져올 수 있다.
```javascript
// components/ExampleDialog.js
import { useState } from 'react'
import { Dialog } from '@reach/dialog'
import '@reach/dialog/styles.css'

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false)
  const open = () => setShowDialog(true)
  const close = () => setShowDialog(false)

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>Hello there. I am a dialog</p>
      </Dialog>
    </div>
  )
}
```

## Component-Level CSS 추가

- '[name].module.css' 파일 명명 규칙을 사용하는 CSS 모듈을 지원

- CSS 모듈은 고유한 클래스 이름을 자동으로 생성해 로컬에서 CSS 범위를 지정 ->  다른 파일에서 동일한 CSS 클래스 이름을 사용가능

- application 안 어디에서나 CSS 모듈 파일을 import 가능

- Ex) Button component에 css적용

  > components/Button.module.css

  ```javascript
  /*
  You do not need to worry about .error {} colliding with any other `.css` or
  `.module.css` files!
  */
  .error {
    color: white;
    background-color: red;
  }
  Then, create components/Button.js, importing and using the above CSS file:
  
  import styles from './Button.module.css'
  
  export function Button() {
    return (
      <button
        type="button"
        // Note how the "error" class is accessed as a property on the imported
        // `styles` object.
        className={styles.error}
      >
        Destroy
      </button>
    )
  }
  ```

  > components/Button.js

  ```javascript
  import styles from './Button.module.css'
  
  export function Button() {
    return (
      <button
        type="button"
        // Note how the "error" class is accessed as a property on the imported
        // `styles` object.
        className={styles.error}
      >
        Destroy
      </button>
    )
  }
  ```

  - CSS module은 선택적으로 사용할 수 있는 기능
  - 확장자가 '.module.css'인 파일만 사용 가능(component단위 적용)
  - 일반 <link> stylesheet와 global CSS파일은 계속 지원됨
  - 프로덕션에서 모든 CSS Module 파일들은 자동으로 경량화되고 '.css' file들의 코드가 분할되어 연결된다.



## Sass 지원

```javascript
npm install sass
```



## Sass 기능 최적화

- 'next.config.js'에 'sassOptions'를 사용해 Sass compiler 설정 가능

  > includePaths 

  ```javascript
  const path = require('path')
  
  module.exports = {
    sassOptions: {
      includePaths: [path.join(__dirname, 'styles')],
    },
  }
  ```

  





## Less 및 Stylus 지원

- '.less' or '.styl' 파일을 사용하기 위해서 import할 것
  - @zeit / next-less
  - @zeit / next-stylus
- less plugin을 사용하는 경우 less에 종속성을 추가해야함



## CSS-in-JS

1. 인라인 스타일로 JS안에 css를 적용

   - Ex

   ```javascript
   function HiThere() {
       return <p style={{color: 'red' }}>hi there</p>
   }
   
   export default HiThere
   ```



2. 특정 고립된 범위의 CSS를 지원하기 위해 styled-jsx를 bundle로 제공

   - 서버렌더링 지원X, JS전용인 웹 컴포넌트와 유사한 'shadow CSS'지원

   - Ex

   ```javascript
   function HelloWorld() {
     return (
       <div>
         Hello world
         <p>scoped!</p>
         <style jsx>{`
           p {
             color: blue;
           }
           div {
             background: red;
           }
           @media (max-width: 600px) {
             div {
               background: blue;
             }
           }
         `}</style>
         <style global jsx>{`
           body {
             background: black;
           }
         `}</style>
       </div>
     )
   }
   
   export default HelloWorld
   ```

   

