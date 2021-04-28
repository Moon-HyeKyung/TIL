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
- 
